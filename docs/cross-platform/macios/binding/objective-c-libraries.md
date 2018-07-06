---
title: Привязка библиотек Objective-C
description: Этот документ содержит обзор создания привязки C# для кода Objective-C, описывающие для привязки события, методы, пользовательские элементы управления и многое другое.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: 4c414e0e863f44045473a248576a3612b1719559
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854835"
---
# <a name="binding-objective-c-libraries"></a>Привязка библиотек Objective-C

При работе с Xamarin.iOS и Xamarin.Mac, возможны случаи, где вы хотите использовать сторонние библиотеки Objective-C. В такой ситуации можно использовать проекты привязки Xamarin для создания привязки C# в собственные библиотеки Objective-C. Проект использует те же средства, которые мы используем для реализуйте iOS и Mac API-интерфейсы в C#.

В этом документе описывается привязка Objective-C API, при связывании просто C API-интерфейсы, для этого следует использовать стандартный механизм .NET [framework P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/).
Сведения о том, как статическое связывание библиотеки доступны на [связывание собственные библиотеки](~/ios/platform/native-interop.md) страницы.

См. в разделе наших companion [привязки справочное руководство по типы](~/cross-platform/macios/binding/binding-types-reference.md).
Кроме того, если вы хотите узнать больше о что происходит за кулисами, проверьте наших [Общие сведения о привязке](~/cross-platform/macios/binding/overview.md) страницы.

Привязки могут быть нацелены на iOS и Mac библиотеки.
Этой странице описывается, как работать с iOS привязки, однако привязки Mac очень похожи.

**Пример кода для iOS**

Можно использовать [iOS пример привязки](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) проект, чтобы поэкспериментировать с привязками.

<a name="Getting_Started" />

## <a name="getting-started"></a>Начало работы

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Самый простой способ создания привязки является создание проекта Xamarin.iOS привязки.
Это можно сделать из Visual Studio для Mac, выбрав тип проекта, **iOS > Библиотека > Библиотека привязок**:

[![](objective-c-libraries-images/00-sml.png "Сделать из Visual Studio для Mac, выбрав тип проекта, библиотека библиотека привязок iOS")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Самый простой способ создания привязки является создание проекта Xamarin.iOS привязки.
Выберите тип проекта, это можно сделать из Visual Studio на Windows **Visual C# > iOS > Библиотека привязок (iOS)**:

[![](objective-c-libraries-images/00vs-sml.png "Библиотека привязок iOS iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Примечание: Привязка проектов для **Xamarin.Mac** поддерживаются только в Visual Studio для Mac.

-----

Созданный проект содержит небольшой шаблон, который можно редактировать, он содержит два файла: `ApiDefinition.cs` и `StructsAndEnums.cs`.

`ApiDefinition.cs` Здесь вы определяете контракта API-интерфейса, это файл, который описывает, как основной API Objective-C проецируется в C#. Синтаксис и содержимое этого файла — это главная Тема обсуждения настоящего документа и ее содержимое ограничены интерфейсы в C# и объявлений делегатов в C#. `StructsAndEnums.cs` Это файл, где необходимо добавить какие-либо определения, необходимые интерфейсы и делегаты. Сюда входят значения перечисления и структуры, которые код может использовать.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Привязка API

Чтобы сделать привязку комплексное, требуется понимать определения API Objective-C и ознакомиться с правилам разработки .NET Framework.

Привязка библиотеки обычно сначала с помощью файла определения API. Файл с определением API — это просто C# исходный файл, содержащий интерфейсы в C# с созданными заметками с небольшим количеством атрибутов, которые помогают управлять привязкой.  Этот файл является то, что определяет контракт между C# и Objective-C является.

Например это файл тривиальные api для библиотеки:

```csharp
using Foundation;

namespace Cocos2D {
  [BaseType (typeof (NSObject))]
  interface Camera {
    [Static, Export ("getZEye")]
    nfloat ZEye { get; }

    [Export ("restore")]
    void Restore ();

    [Export ("locate")]
    void Locate ();

    [Export ("setEyeX:eyeY:eyeZ:")]
    void SetEyeXYZ (nfloat x, nfloat y, nfloat z);

    [Export ("setMode:")]
    void SetMode (CameraMode mode);
  }
}
```

Приведенный выше пример определяется класс с именем `Cocos2D.Camera` , наследуемый от класса `NSObject` базовый тип (этот тип получается из `Foundation.NSObject`) и который определяет статическое свойство (`ZEye`), два метода, которые принимают аргументы и метод, которые принимает три аргументы.

Подробное обсуждение формата файла API и атрибуты, которые можно использовать рассматривается в [файл определения API](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) разделе ниже.

Чтобы создать полный привязки, будет имею дело с четырех компонентов:

-  Файл определения API (`ApiDefinition.cs` в шаблоне).
-  Необязательно: любые перечислимые типы, типы, структуры, необходимые в файле определения API (`StructsAndEnums.cs` в шаблоне).
-  Необязательно: Дополнительные источники, которые могут разверните созданную привязку, или предоставляют дополнительные C# понятное API (все файлы C#, добавляемые в проект).
-  Собственная библиотека, которому выполняется привязка.

На этой диаграмме показана связь между файлами.

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "Эта диаграмма показывает связи между файлами")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

Файл определения API будет содержать только определения пространства имен и интерфейс (с помощью любого элемента, интерфейс может содержать) и не должен содержать классы, перечисления, делегаты или структуры. Файл определения API — это просто контракт, который будет использоваться для создания API.

Любой дополнительный код, что требуется, например перечисления или вспомогательных классов должны размещаться в отдельном файле, в примере выше «CameraMode» является значением перечисления, который отсутствует в CS-файле и должны размещаться в отдельном файле, например `StructsAndEnums.cs` :

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

`APIDefinition.cs` Файл объединяется с `StructsAndEnum` класса и используются для создания привязки core библиотеки. Вы можете использовать итоговый библиотеку как-является, но как правило, будет необходимо настроить библиотеку итоговый добавить некоторые функции C# для пользователей. Некоторые примеры реализации `ToString()` метод, предоставляют индексаторах в C# добавьте неявные преобразования в и из некоторые собственные типы или предоставляют версии некоторые методы со строгой типизацией. Эти улучшения, хранятся в дополнительных файлов C#. Просто добавьте в проект файлов C#, и они будут включены в этот процесс сборки.

Это показывает, как можно реализовать в коде на вашей `Extra.cs` файл. Обратите внимание на то, что вы будете использовать разделяемые классы как эти дополнения разделяемые классы, которые формируются из сочетания `ApiDefinition.cs` и `StructsAndEnums.cs` основы привязки:

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

Создание библиотеки будет создавать собственные привязки.

Чтобы завершить этой привязкой, следует добавить собственной библиотеки в проект.  Это можно сделать путем добавления собственной библиотеки в проект, либо перетащив собственной библиотеки из Finder в проект в обозревателе решений, либо, щелкнув правой кнопкой мыши проект и выбрав **добавить**  >  **Добавить файлы** для выбора собственной библиотеки.
Собственные библиотеки по соглашению, начинающихся со слова «lib» и заканчиваться расширением «.a». После этого Visual Studio для Mac добавить два файла: файл .a и автоматически заполненный файл C#, содержащий сведения о собственной библиотеки содержит:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Собственные библиотеки по соглашению, начинаться с word lib и заканчиваться .a расширения")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

Содержимое `libMagicChord.linkwith.cs` файл содержит сведения об использовании этой библиотеки и указывает, что интегрированная СРЕДА для упаковки этого двоичного файла в файле DLL:

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Полные сведения об использовании [ `[LinkWith]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) атрибут описаны в [привязки справочное руководство по типы](~/cross-platform/macios/binding/binding-types-reference.md).

Теперь при сборке проекта вы получите `MagicChords.dll` файл, содержащий привязки и собственной библиотеки. Этот проект можно распространять или использовать итоговому файлу DLL другими разработчиками для собственных.

Иногда требуется несколько значений перечисления, определениях делегатов или других типов. Не размещайте их в файл определения API, так как это просто контракт

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>Файл определения API

Файл определения API состоит из нескольких интерфейсов. Интерфейсы в определении API будет преобразовано в объявлении класса, и должен быть снабжен атрибутом [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) атрибут для указания базового класса для класса.

Вы можете спросить, почему мы не использовали классы вместо интерфейсов для определения контракта. Мы выбрали интерфейсы, так как он позволил нам для записи контракт для метода без необходимости указания тела метода в файле определения API и без необходимости объявлять тело, которые исключение или возвращает осмысленное значение.

Но так как мы используем интерфейс в качестве основы для создания класса, нам пришлось прибегнуть к Декорирование различные части контракта с атрибутами для привязки.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Методы привязки

— Это самый простой привязки, которые можно выполнить для привязки метода. Просто объявите метод в интерфейсе с помощью C# соглашения об именовании и примените к методу [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) атрибута. [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) Атрибут является то, какие связи имя C# с именем Objective-C в среде выполнения с Xamarin.iOS. Параметр [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) атрибута является имя селектора Objective-C. Ниже представлено несколько примеров.

```csharp
// A method, that takes no arguments
[Export ("refresh")]
void Refresh ();

// A method that takes two arguments and return the result
[Export ("add:and:")]
nint Add (nint a, nint b);

// A method that takes a string
[Export ("draw:atColumn:andRow:")]
void Draw (string text, nint column, nint row);
```

Выше примеры показывают, как связать методы экземпляра. Чтобы привязать статические методы, необходимо использовать [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) атрибут, следующим образом:

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Это необходимо, так как контракт является частью интерфейса, и интерфейсы не имеют представления о объявления экземпляра и статическом, поэтому необходимо еще раз прибегнуть к атрибутам. Если вы хотите скрыть конкретного метода из привязки, вы можете декорировать метод с [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) атрибута.

`btouch-native` Представлена команда проверяет наличие ссылочных параметров не равняется null. Если вы хотите разрешить значения null для определенного параметра, используйте [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) атрибут в параметре, следующим образом:

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

При экспорте ссылочным типом, с помощью [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) ключевое слово, можно также указать семантику выделения. Это необходимо, чтобы гарантировать, что данные не происходит утечка.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Свойства привязки

Так же, как методы, свойства Objective-C связаны с помощью [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) атрибут и карты напрямую в свойства C#. Так же, как методы, свойства можно декорировать с [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) и [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) атрибуты.

При использовании [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) для свойства в деле btouch собственный атрибут фактически выполняет привязку два метода: метод считывания и метод задания. Имя, которое следует указать для экспорта, **basename** и метод задания значения вычисляется путем добавления слова «set», включение первую букву **basename** в верхний регистр и делая селекторе занять аргумент. Это означает, что `[Export ("label")]` на свойство фактически выполняет привязку «label» и «setLabel:» Objective-C методы.

Иногда свойства Objective-C не соответствуют описанной выше схемы и имя вручную перезаписывается. В таких случаях можно управлять способом, что привязка создается с помощью [ `[Bind]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) атрибут метода получения или задания, например:

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

Значение привязывается «isMenuVisible» и «setMenuVisible:». При необходимости свойство можно привязать, используя следующий синтаксис:

```csharp
[Category, BaseType(typeof(UIView))]
interface UIView_MyIn
{
  [Export ("name")]
  string Name();

  [Export("setName:")]
  void SetName(string name);
}
```

Где Get и set явно определены как в `name` и `setName` привязок выше.

В дополнение к поддержке для статических свойств, с помощью [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute), вы можете дополнить свойства статического потока с [ `[IsThreadStatic]` ](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute), например:

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Так же, как методы позволяют некоторые параметры для с флагами [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute), можно применить [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) на свойство, чтобы указать значение null является допустимым значением для свойства, например:

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) Непосредственно в метод задания значения также можно указать параметр:

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Особенности привязки пользовательских элементов управления

При настройке привязки для пользовательского элемента управления, следует учитывать следующие факторы:

1. **Свойства привязки должны быть статическими** — при определении привязки свойств, [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) атрибут должен использоваться.
 2. **Имена свойств должны точно соответствовать** -имя, используемое для привязки свойства должно соответствовать имени свойства в пользовательском элементе управления точно.
3. **Типы свойств должны точно соответствовать** -тип переменной, используемый для привязки свойства точно совпадать с типом свойства в пользовательский элемент управления.
4. **Точки останова и getter/setter** — поместить точки останова в метод получения значения или никогда не будет достигнута методов задания свойства.
5. **Обратите внимание, обратные вызовы** -необходимо будет использовать обратные вызовы наблюдения для получения уведомлений об изменениях в значениях свойств пользовательских элементов управления.

Чтобы наблюдать за любой из перечисленных выше предупреждения приведет привязки, автоматически сбой во время выполнения.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Изменяемый шаблон Objective-C и свойства

Objective-C платформы используют идиома, где некоторые классы являются неизменяемыми изменяемый подклассом. Например `NSString` является неизменяемым версией, хотя `NSMutableString` подклассом, который допускает изменение.

В этих классах часто можно увидеть неизменяемый базовый класс содержит свойства с помощью метода получения, но не метод задания. И для изменяемых версии для ознакомления метод задания. Так как это действительно невозможно с помощью C#, нам пришлось сопоставить эту идиому идиома, который будет работать с помощью C#.

Способом, что оно сопоставлено с C# является добавление метод считывания и метод задания значения в базовом классе, но Пометка setter с [ `[NotImplemented]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute) атрибута.

Затем на изменяемый подкласса, используйте [ `[Override]` ](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) атрибут в свойство, чтобы убедиться, что свойство является фактически переопределение поведения родительского элемента.

Пример

```csharp
[BaseType (typeof (NSObject))]
interface MyTree {
    string Name { get; [NotImplemented] set; }
}

[BaseType (typeof (MyTree))]
interface MyMutableTree {
    [Override]
    string Name { get; set; }
}
```

<a name="Binding_Constructors" />

### <a name="binding-constructors"></a>Конструкторы привязки

`btouch-native` Средство автоматически создаст четверки конструкторов в классе, для данного класса `Foo`, он создает:

-  `Foo ()`: конструктор по умолчанию (сопоставленное значению «init» конструктор Objective-C)
-  `Foo (NSCoder)`: конструктор, используемый во время десериализации файлов NIB (сопоставляется Objective-C «initWithCoder:» конструктор).
-  `Foo (IntPtr handle)`: конструктор для создания дескриптора на основе, он вызывается средой выполнения при среда выполнения должна предоставлять управляемого объекта из неуправляемого объекта.
-  `Foo (NSEmptyFlag)`: используется производными классами для предотвращения double инициализации.

Для конструкторов, которые указываются, они должны быть объявлены с помощью следующую сигнатуру в определении интерфейса: они должны возвращать `IntPtr` значение и имя метода должен быть конструктор. Например привязать `initWithFrame:` конструктор, это использовалась бы:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Привязки протоколов

Как описано в документе разработки API, см. в разделе [обсуждении моделей и протоколы](~/ios/internals/api-design/index.md#Models), Xamarin.iOS сопоставляет протоколы Objective-C в классы, которые помечены с [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) атрибут. Обычно это используется при реализации классов делегатов Objective-C.

Разница между обычный связанный класс и класс делегата является то, что класс делегата может иметь один или несколько вспомогательных методов.

Для примера рассмотрим `UIKit` класс `UIAccelerometerDelegate`, это, как он привязан в Xamarin.iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Так как это необязательного метода в определении `UIAccelerometerDelegate` больше делать ничего. Но если необходимый метод на протоколе, следует добавить [ `[Abstract]` ](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute) к методу атрибут. Это заставит пользователя реализации для предоставления тела метода.

Как правило используются протоколы в классах, которые отвечают на сообщения. Обычно это делается в Objective-C путем назначения свойству «delegate» экземпляр объекта, который отвечает на методы в протоколе.

Соглашения для Xamarin.iOS является поддержка обоих Objective-C слабосвязанных стиль там, где любой экземпляр `NSObject` можно назначить делегата, а также также предоставляют со строгой типизацией его версия. По этой причине мы обычно предоставляют оба `Delegate` свойство, которое является строго типизированным и `WeakDelegate` , является слабо типизированным. Мы обычно привязать слабо типизированной версии с [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute), и мы используем [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) атрибут, чтобы указать версию со строгой типизацией.

Это показывает, как мы привязаны `UIAccelerometer` класса:

```csharp
[BaseType (typeof (NSObject))]
interface UIAccelerometer {
        [Static] [Export ("sharedAccelerometer")]
        UIAccelerometer SharedAccelerometer { get; }

        [Export ("updateInterval")]
        double UpdateInterval { get; set; }

        [Wrap ("WeakDelegate")]
        UIAccelerometerDelegate Delegate { get; set; }

        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }
}
```

<a name="iOS7ProtocolSupport" />

**Новые возможности MonoTouch 7.0**

Начиная с версии 7.0 MonoTouch протокол новых и улучшенных возможностей привязки был включен.  Эта новая поддержка упрощает использование идиомы Objective-C для внедрения одного или нескольких протоколов в одном классе.

Для каждого определения протокола `MyProtocol` в Objective-C, теперь есть `IMyProtocol` интерфейс, который содержит все необходимые методы от протокола, а также класс расширений, который предоставляет все методы, необязательно.  Выше, в сочетании с новой поддержки в Xamarin Studio, редактор позволяет разработчикам реализовать методы протокола без использования отдельной подклассы предыдущих абстрактной модели классов.

Любое определение, которое содержит [ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) атрибут на самом деле создаст три вспомогательных классов, которые значительно расширяют способ предоставления протоколы:

```csharp
    // Full method implementation, contains all methods
    class MyProtocol : IMyProtocol {
        public void Say (string msg);
        public void Listen (string msg);
    }

    // Interface that contains only the required methods
    interface IMyProtocol: INativeObject, IDisposable {
        [Export ("say:")]
        void Say (string msg);
    }

    // Extension methods
    static class IMyProtocol_Extensions {
        public static void Optional (this IMyProtocol this, string msg);
        }
    }
```

**Реализацию класса** предоставляет полный абстрактный класс, можно переопределить отдельные методы и получить полный тип безопасности.  Однако из-за C# не поддерживает множественное наследование, существуют сценарии, где можно было бы должен иметь другой базовый класс, но по-прежнему необходимо реализовать интерфейс, который там, где

Созданный **определение интерфейса** пригодиться.  Это интерфейс, который содержит все необходимые методы от протокола.  Это позволяет разработчикам, которые необходимо реализовать протокол просто реализовать интерфейс.  Среда выполнения автоматически зарегистрирует тип как внедрение протокола.

Обратите внимание на то, что интерфейс только перечислены необходимые методы и предоставлять необязательные методы.  Это означает, что классы, применяющие протокол получите полную проверку типов для требуемых методов, но придется прибегнуть к слабой типизацией (вручную с помощью [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) атрибуты и соответствия сигнатуры) необязательного протокол методами.

Для удобства использования API, который использует протоколы средство привязки также создаст класс метода расширения, который предоставляет все необязательные методы.  Это означает, что до тех пор, пока для использования API, можно рассматривать как имеющий все методы протоколы.

Если вы хотите использовать определения протокола в API, необходимо написать скелет пустых интерфейсов в определении API.  Если вы хотите использовать MyProtocol в интерфейсе API, потребуется это сделать:

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

Оно необходимо, поскольку во время привязки `IMyProtocol` бы не существует, то есть почему нужно указать пустой интерфейс.

#### <a name="adopting-protocol-generated-interfaces"></a>Внедрение сформированные протокола интерфейсы

Каждый раз, когда вы реализовывать один из интерфейсов, созданных для протоколов, следующим образом:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Реализацию для методов интерфейса автоматически экспортируются соответствующее имя, поэтому она эквивалентна следующей:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Не важно, если интерфейс реализован в явно или неявно.

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>Класс расширений привязки

В Objective-C является возможность расширения классов при помощи новых методов, сохранят дух методов расширения в C#. При наличии одного из этих методов можно использовать [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) атрибут, чтобы пометить метод как получатель сообщения Objective-C.

Например, в Xamarin.iOS мы привязаны методы расширения, определенные для `NSString` при `UIKit` импортируется в виде методов `NSStringDrawingExtensions`, следующим образом:

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Привязка Objective-C списки аргументов

Objective-C поддерживает переменное число аргументов. Пример:

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Для вызова данного метода из C#, требуется создать подпись следующим образом:

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Этот код объявляет метод как внутренние, скрытие приведенный выше API от пользователей, но предоставление ее в библиотеку. Вы можете написать метод следующим образом:

```csharp
public void AppendWorkers(params Worker[] workers)
{
    if (workers == null)
         throw new ArgumentNullException ("workers");

    var pNativeArr = Marshal.AllocHGlobal(workers.Length * IntPtr.Size);
    for (int i = 1; i < workers.Length; ++i)
        Marshal.WriteIntPtr (pNativeArr, (i - 1) * IntPtr.Size, workers[i].Handle);

    // Null termination
    Marshal.WriteIntPtr (pNativeArr, (workers.Length - 1) * IntPtr.Size, IntPtr.Zero);

    // the signature for this method has gone from (IntPtr, IntPtr) to (Worker, IntPtr)
    WorkerManager.AppendWorkers(workers[0], pNativeArr);
    Marshal.FreeHGlobal(pNativeArr);
}
```

<a name="Binding_Fields" />

### <a name="binding-fields"></a>Привязка полей

Иногда требуется получить доступ к открытых полей, которые были объявлены в библиотеке.

Обычно эти поля содержат значения строками или целыми числами, которые должна указывать ссылка. Обычно они используются как строка, представляющая отправки определенных уведомлений и как ключи в словарях.

Чтобы привязать поле, добавьте свойство в файл определения интерфейса и снабдить свойство с [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) атрибута. Этот атрибут принимает один параметр: имя C символа для уточняющего запроса. Пример:

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Если вы хотите заключить в статический класс, который является производным от различных полях `NSObject`, можно использовать [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) атрибут класса, следующим образом:

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

Выше создаст `LonelyClass` которого является производным от `NSObject` и будет содержать привязку к `NSSomeEventNotification` 
 `NSString` в виде `NSString`.

[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) Атрибут может применяться для следующих типов данных:

-  `NSString` ссылки (только свойства только для чтения)
-  `NSArray` ссылки (только свойства только для чтения)
-  32-разрядных целых чисел (`System.Int32`)
-  64-разрядных целых чисел (`System.Int64`)
-  32-разрядные (`System.Single`)
-  64-разрядных значения с плавающей запятой (`System.Double`)
-  `System.Drawing.SizeF`
-  `CGSize`

В дополнение к имени собственного поля можно указать имя библиотеки, где находится поле, передав имя библиотеки:

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

При связывании статически, нет, нет библиотеки для привязки, поэтому вам нужно использовать `__Internal` имя:

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Привязка перечисления

Вы можете добавить `enum` непосредственно в привязке файлов упрощает использовать внутри определений API - без использования различных исходных файла (который должен быть скомпилирован в окончательной проекции и привязки).

Пример

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

Можно также создать свои собственные перечисления для замены `NSString` константы. В этом случае будет генератор **автоматически** создать методы для преобразования значения перечислений и NSString константы для вас.

Пример

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}

interface MyType {
    [Export ("performForMode:")]
    void Perform (NSString mode);

    [Wrap ("Perform (mode.GetConstant ())")]
    void Perform (NSRunLoopMode mode);
}
```

В приведенном выше примере можно снабдить `void Perform (NSString mode);` с [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) атрибута. Именно она **скрыть** API на основе константы из потребителей привязки.

Тем не менее это позволит ограничить использование подклассов тип как лучше API использует альтернативный [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) атрибута. Эти созданные методы не являются `virtual`, т. е. Вы не сможете переопределить ими, которые могут или не, является хорошим выбором.

Альтернативным вариантом является для пометки исходного, `NSString`-зависимости, определение как `[Protected]`. Таким образом, создание подклассов для работы, при необходимости, и версии wrap'ed по-прежнему будет работать и вызов переопределенного метода.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Привязка `NSValue`, `NSNumber`, и `NSString` лучше типу

[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) Атрибут допускает привязку `NSNumber`, `NSValue` и `NSString`(перечисления) в более точные типы C#. Атрибут может использоваться для создания лучшую, более точным, API-интерфейса .NET через собственные API.

Вы можете дополнить методы (возвращаемое значение), параметры и свойства с [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute). Единственным ограничением является то, что член **не должны** находиться внутри [ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) или [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) интерфейс.

Пример:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Пример кода выведет данные:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Внутри мы сделаем `bool?`  <->  `NSNumber` и `CGRect`  <->  `NSValue` преобразования.

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) также поддерживает массивы `NSNumber` `NSValue` и `NSString`(перечисления).

Пример:

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

Пример кода выведет данные:

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` — `NSString` резервного enum, получает право `NSString` значение и обработки преобразования типа.

См. в разделе [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) документации, чтобы просмотреть поддерживаемые преобразования типов.

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Привязка уведомлений

Уведомления — это сообщения, которые учитываются в `NSNotificationCenter.DefaultCenter` и используются как механизм для широковещательной передачи сообщений из одной части приложения в другое. Разработчикам подписаться на уведомления, обычно используя [NSNotificationCenter](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/) [AddObserver](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/M/AddObserver/) метод. Когда приложение отправляет сообщение в центр уведомлений, оно обычно содержит полезные данные, хранящиеся в [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) словаря. Этот словарь нестрого типизирован, и получения информации от его подвержено ошибкам, поскольку пользователям обычно нужно прочитать в документации, какие ключи доступны в словаре и типы значений, которые могут храниться в словаре. Наличие ключей иногда используется в качестве также логического значения.

Генератор привязки Xamarin.iOS предоставляет поддержку для разработчиков привязывать уведомления. Чтобы сделать это, необходимо задать [ `[Notification]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute) атрибут для свойства, которое также были помечены [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) свойств (он может быть общедоступный или частный).

Этот атрибут может использоваться без аргументов для уведомлений, которые содержат полезные данные не найдены, или можно указать `System.Type` , ссылающийся на другой интерфейс в определении API, обычно с именем, заканчивающимся «EventArgs». Генератор приводит к отключению интерфейса в класс, который наследуется от класса `EventArgs` и будет включать все свойства в списке. [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) Атрибут должен использоваться в классе EventArgs в списке имя ключа, используемый для поиска словаря Objective-C, чтобы получить значение.

Пример:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

Приведенный выше код будет создать вложенный класс `MyClass.Notifications` со следующими методами:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Пользователи кода можно затем легко подписаться на уведомления отправляется [NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/) с помощью следующего кода:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

Значение, возвращаемое `ObserveDidStart` может использоваться в легко прекратить получение уведомлений, следующим образом:

```csharp
token.Dispose ();
```

Или можно вызвать [NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject/) и передать маркер. Уведомление содержит параметры, следует указать вспомогательный объект `EventArgs` интерфейса следующим образом:

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

Выше создаст `MyScreenChangedEventArgs` класса `ScreenX` и `ScreenY` свойства, которые получает данные из [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) словарь с помощью ключей «ScreenXKey» и «ScreenYKey» соответственно и применить соответствующие преобразования. `[ProbePresence]` Атрибут используется для генератора для выборки данных, если задано значение ключа `UserInfo`, вместо того, чтобы извлечь значение. Используется для случаев, когда наличие ключа является значение (обычно для логических значений).

Это позволяет писать код следующим образом:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>Категории привязки

Категории представляют собой механизм Objective-C, позволяет расширить набор методов и свойств, доступных в классе.   На практике они используются для расширения функциональности базового класса (например `NSObject`) при конкретной платформы связана в последовательности (например `UIKit`), что делает их методы доступны, но только в том случае, если новая платформа связана в последовательности.   В некоторых других случаях они используются для организации функций в классе по функциональным возможностям.   Они аналогичны дух методов расширения в C#. Это, как будет выглядеть категорию в Objective-C:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

Приведенный выше пример Если на библиотеку распространится экземпляров `UIView` с помощью метода `makeBackgroundRed`.

Чтобы привязать их, можно использовать [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) атрибут в определении интерфейса.  При использовании [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) атрибута, значение [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) атрибут изменяет не могут использоваться для указания базового класса для расширения, должно быть типа для расширения.

Показано как `UIView` расширения привязки и преобразуются в методы расширения в C#:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Будет создан `MyUIViewExtension` класс, содержащий `MakeBackgroundRed` метода расширения.  Это означает, что теперь можно вызвать «MakeBackgroundRed» на любом `UIView` подкласс, предоставляя те же функциональные возможности, можно получить на Objective-C. В некоторых других случаях категории используются не для того, чтобы расширить класс системы, но для объединения функций, исключительно для целей оформления.  Пример:

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Несмотря на то, что можно использовать [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) атрибут также для этого стиля оформления, объявлений, можно также просто добавить их все в определении класса.  Оба эти бы те же:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Twitter {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Facebook {
    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

Он не только в таких случаях для слияния категории:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);

    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

<a name="Binding_Blocks" />

### <a name="binding-blocks"></a>Блоки привязки

Блоки являются новую конструкцию, представленное в Apple, чтобы перенести функционального аналога анонимные методы C# в Objective-C. Например `NSSet` класс теперь предоставляет этот метод:

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

Данному описанию объявляет метод, вызванный `enumerateObjectsUsingBlock:` , принимающий один аргумент с именем `block`. Этот блок аналогична анонимного метода в C#, у нее есть поддержка записи текущей среды (указатель «this», доступ к локальным переменным и параметрам). Приведенный выше метод в `NSSet` вызывает блок с двумя параметрами `NSObject` ( `id obj` части) и указатель на логическое значение ( `BOOL *stop`) части.

Чтобы привязать такого рода API с btouch, необходимо сначала объявить сигнатура типа блока, как C# делегата и затем ссылаться на него из точки входа интерфейса API, следующим образом:

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

И теперь ваш код может вызывать функции из C#:

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

При желании можно также использовать лямбда-выражения:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>Асинхронные методы

Генератор привязки можно превратить определенного класса методы в понятных асинхронные методы (методы, возвращающие задачи или задачи&lt;T&gt;).

Можно использовать [ `[Async]` ](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) атрибут на методы, возвращающие void и имеющий аргумент последнего обратного вызова.  Когда вы примените к методу, привязки генератору формировать версии этого метода с суффиксом `Async`.  Если обратный вызов не принимает никаких параметров, возвращаемое значение будет `Task`, если обратный вызов принимает параметр, результат будет `Task<T>`.  Если обратный вызов принимает несколько параметров, следует задать `ResultType` или `ResultTypeName` указать желаемое имя созданного типа, которая будет содержать все свойства.

Пример

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

Приведенный выше код создает обе метод LoadFile, а также:

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Отображая строгих типов для параметров слабого NSDictionary

Во многих местах в Objective-C API, параметры передаются как слабо типизированной `NSDictionary` API-интерфейсы с конкретными ключами и значения, но они являются подвержено ошибкам, (вы можете передать недопустимые ключи и получить предупреждения не; вы можете передать недопустимые значения и получить без предупреждений) и нервов для использования, поскольку для них требуется несколько обращений к документации для поиска возможных имена ключей и значений.

Решение — предоставить версию со строгой типизацией, которая предоставляет строго типизированную версию API-интерфейса и за кулисами сопоставляет различных базовых ключей и значений.

Так например, если принят Objective-C API `NSDictionary` и оно задокументировано как принимающий ключ `XyzVolumeKey` выполняется `NSNumber` с тома значение от 0,0 до 1.0 и `XyzCaptionKey` , принимает строку, нужно предоставить пользователям удобный API Это выглядит следующим образом:

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

`Volume` Свойство определено как допускающее значение NULL, как соглашение на Objective-C не требует эти словари, должны иметь значения, поэтому существуют сценарии, где может не иметь значение.

Чтобы сделать это, вам потребуется выполнить ряд действий.

* Создайте класс со строгой типизацией, который наследуется от класса [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) и предоставляет различные методы получения и задания для каждого свойства.
* Объявления перегрузки для методов, принимающих `NSDictionary` вступили в новую версию со строгой типизацией.

Можно создать строго типизированный класс можно вручную или использовать генератор для выполнения работы для вас.  Сначала мы рассмотрим, как это сделать вручную, чтобы понять, что происходит, а затем автоматической настройке.

Необходимо создать вспомогательный файл для этого, он не входит в ваш контракт API.  Это то, что вам пришлось бы писать для создания класса XyzOptions:

```csharp
public class XyzOptions : DictionaryContainer {
# if !COREBUILD
    public XyzOptions () : base (new NSMutableDictionary ()) {}
    public XyzOptions (NSDictionary dictionary) : base (dictionary){}

    public nfloat? Volume {
       get { return GetFloatValue (XyzOptionsKeys.VolumeKey); }
       set { SetNumberValue (XyzOptionsKeys.VolumeKey, value); }
    }
    public string Caption {
       get { return GetStringValue (XyzOptionsKeys.CaptionKey); }
       set { SetStringValue (XyzOptionsKeys.CaptionKey, value); }
    }
# endif
}
```

Затем необходимо предоставить метода оболочки, используемый для вызова API высокого уровня, на основе API низкого уровня.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Если API не будут перезаписаны, можно безопасно скрыть API на основе NSDictionary с помощью [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) атрибута.

Как вы видите, мы используем [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) атрибута для отображения новой точки входа интерфейса API, и делается с помощью наших со строгой типизацией `XyzOptions` класса.  Метод оболочки также допустимо значение null для передачи.

Теперь кое-что мы не рассказывать о, где `XyzOptionsKeys` поступили значения.  Обычно можно сгруппировать ключи, API-Интерфейс, используемый в статическом классе как `XyzOptionsKeys`, следующим образом:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Рассмотрим автоматическая поддержка создания эти словари со строгой типизацией.  Это позволяет избежать немало шаблонного и словаря можно определить непосредственно в контракте API, а не с помощью внешнего файла.

Чтобы создать словарь со строгой типизацией, вводят интерфейса в API и установить для него с помощью [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) атрибута.  Это означает, что генератор должен создать класс с тем же именем, как ваш интерфейс, который будут производными от `DictionaryContainer` и предоставляет надежный типизированные методы доступа для него.

[ `[StrongDictionary]` ](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) Атрибут принимает один параметр — имя статического класса, содержащий ключи словаря.  Затем каждое свойство интерфейса станет строго типизированных методов доступа.  По умолчанию код будет использовать имя свойства с суффиксом «Ключа» в статическом классе для создания метода доступа.

Это означает, что создание строго типизированных методов доступа больше не требуется внешний файл, а также того, чтобы вручную создавать методы get и set для каждого свойства, и необходимости поиска ключей вручную самостоятельно.

Это, что все привязки выглядит следующим образом:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
[StrongDictionary ("XyzOptionKeys")]
interface XyzOptions {
    nfloat Volume { get; set; }
    string Caption { get; set; }
}

[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

При необходимости для ссылки в вашей `XyzOption` члены другое поле (то есть не имя свойства с суффиксом `Key`), можно снабдить свойство с [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) атрибут с именем, хотите использовать.

<a name="Type_mappings" />

## <a name="type-mappings"></a>Тип сопоставления

В этом разделе рассматриваются в сопоставлении типов Objective-C в типы C#.

<a name="Simple_Types" />

### <a name="simple-types"></a>Простые типы

В следующей таблице показаны, как вам нужно сопоставить типы из Objective-C и в мире платформе CocoaTouch во всем мире Xamarin.iOS:

|Имя типа Objective-C|Тип Unified API Xamarin.iOS|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([об привязки NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (см. также: [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|Типы CoreFoundation (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|Типы Foundation (`NS*`)|`Foundation.NS*`|
|`id`|`Foundation`.`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays" />

### <a name="arrays"></a>Массивы

Среда выполнения Xamarin.iOS автоматически берет на себя массивы в C# для преобразования `NSArrays` и выполнением преобразования, так, например метод мнимой Objective-C, который возвращает `NSArray` из `UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

Привязывается следующим образом:

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

Идея является использование массива C# с со строгой типизацией, как это позволит интегрированной среды разработки для предоставления соответствующего кода завершения с фактическим типом без принудительного пользователю угадать, или ознакомьтесь с документацией, чтобы узнать фактический тип объектов, содержащихся в массиве.

В случаях, где можно не отслеживать фактическое наиболее производный тип, содержащихся в массиве, можно использовать `NSObject []` как возвращаемое значение.

<a name="Selectors" />

### <a name="selectors"></a>Селекторы

Селекторы отображаются на Objective-C API как специальный тип `SEL`. При привязке селектора, необходимо установить соответствие типа `ObjCRuntime.Selector`.  Обычно селекторы, представлены в API с помощью объекта, целевой объект и селектор для вызова в целевой объект. По существу предоставляя эти соответствует делегату C#: что-нибудь, который инкапсулирует метод, который вызывается как объект для вызова метода в.

Вот как выглядит привязки:

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

И именно как метод обычно используются в приложении:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        b.SetTarget (this, new Selector ("print"));
    }

    [Export ("print")]
    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

Чтобы сделать привязку лучше для разработчиков на C#, обычно будет предоставляют метод, который принимает `NSAction` параметр, который позволяет C# делегаты и лямбда-выражения для использования вместо `Target+Selector`. Для этого обычно следует скрыть `SetTarget` метод, помечая его с [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) атрибут правой кнопкой мыши и затем предоставит новый вспомогательный метод, следующим образом:

```csharp
// API.cs
interface Button {
   [Export ("setTarget:selector:"), Internal]
   void SetTarget (NSObject target, Selector sel);
}

// Extensions.cs
public partial class Button {
     public void SetTarget (NSAction callback)
     {
         SetTarget (new NSActionDispatcher (callback), NSActionDispatcher.Selector);
     }
}
```

Итак, теперь пользовательским кодом, можно записать следующим образом:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        // First Style
        b.SetTarget (ThePrintMethod);

        // Lambda style
        b.SetTarget (() => {  /* print here */ });
    }

    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

<a name="Strings" />

### <a name="strings"></a>Строки

Во время привязки метода, принимающего `NSString`, можно заменить, что C# строкового типа, на типы возвращаемого значения и параметры.

Единственный случай, когда может потребоваться использовать `NSString` напрямую — когда строка используется в качестве маркера. Дополнительные сведения о строках и `NSString`, прочитайте [проектирование API на NSString](~/ios/internals/api-design/nsstring.md) документа.

В некоторых редких случаях API может предоставлять строку типа C (`char *`) вместо строки Objective-C (`NSString *`). В таком случае можно добавить заметку к параметру [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring) атрибута.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out-параметры ref

Некоторые интерфейсы API возвращают значения параметров, или передавать параметры ссылкой.

Обычно подпись выглядит следующим образом:

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

В первом примере показано распространенных случаях Objective-C, чтобы возвращать коды ошибок, указатель на `NSError` передается указатель, и возвращаемое значение.   Второй метод показывает, как метод Objective-C может получить объект и изменить его содержимое.   Это было бы передачи по ссылке, а не значение чистые выходные данные.

Привязка будет выглядеть следующим образом:

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Атрибуты управления памяти

При использовании [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) атрибут и вы передаете данные, которые будут храниться в вызываемом методе, можно указать аргумент семантики, передавая его как второй параметр, например:

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

Выше будут значение флага как имеющий семантику «Сохранить». Доступные семантика.

-  Назначить
-  Копировать
-  Сохранять

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Рекомендации по стилю

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>С помощью [внутренний]

Можно использовать [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) атрибут, чтобы скрыть метод из открытого API-интерфейса. Вы можете сделать это в случаях, когда предоставляемого API слишком низкого уровня, и вы хотите предоставить высокоуровневая реализация в отдельном файле, на основе этого метода.

Это также можно использовать при ограничениями в генераторе привязки, например некоторых расширенных сценариях может предоставлять типы, которые не привязаны для привязки в тем, как и вы хотите самостоятельно заключить этих типов, в собственный стиль.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Обработчики событий и обратных вызовов

Objective-C классы обычно рассылать уведомления или запросить информацию по отправке сообщения на класс делегата (Objective-C делегата).

Эта модель, пока полностью поддерживается и отображенные с Xamarin.iOS иногда может быть громоздким. Xamarin.iOS предоставляет шаблон на основе событий C# и системе обратного вызова метода в классе, который может использоваться в таких ситуациях. Это позволяет для выполнения следующего кода:

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

Генератор привязки можно уменьшить объем необходимого для сопоставления в формате Objective-C в шаблоне C# ввода.

Начиная с Xamarin.iOS 1.4, он станет возможно проинструктировать генератора для создания привязок для конкретного делегатов Objective-C и предоставить делегат, как C# событий и свойств в типе узла.

Существует два класса, связанных с этим процессом, класс узла, который будет та, которая в настоящее время генерирует события и отправляет их в `Delegate` или `WeakDelegate` и класс фактическому делегату.

Учитывая следующие настройки:

```csharp
[BaseType (typeof (NSObject))]
interface MyClass {
    [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
    NSObject WeakDelegate { get; set; }

    [Wrap ("WeakDelegate")][NullAllowed]
    MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
    [Export ("loaded:bytes:")]
    void Loaded (MyClass sender, int bytes);
}
```

Чтобы поместить класс в оболочку необходимо следующее:

-  В классе узел, добавляемый к [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   объявление типа, выступающего в качестве его делегат и имя C#, который вы предоставили. В приведенном выше примере это `typeof (MyClassDelegate)` и `WeakDelegate` соответственно.
-  В классе делегат для каждого метода, имеющий более двух параметров необходимо указать тип, который вы хотите использовать для автоматически созданного класса EventArgs.

Генератор привязки не ограничивается только одно событие назначения упаковки, это возможно, некоторые классы Objective-C для отправки сообщений в несколько делегатов, поэтому вы должны будете предоставить массивы для поддержки такой конфигурации. Большинство настроек не будет нужен, но генератор готов для поддержки таких случаях.

Результирующий код будет:

```csharp
[BaseType (typeof (NSObject),
    Delegates=new string [] {"WeakDelegate"},
    Events=new Type [] { typeof (MyClassDelegate) })]
interface MyClass {
        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }

        [Wrap ("WeakDelegate")][NullAllowed]
        MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
        [Export ("loaded:bytes:"), EventArgs ("MyClassLoaded")]
        void Loaded (MyClass sender, int bytes);
}
```

`EventArgs` Используется для указания имени `EventArgs` класса. Следует использовать только одну подписи (в этом примере `EventArgs` будет содержать `With` свойство nint типа).

С помощью приведенные выше определения генератор выдаст следующее событие в созданный MyClass:

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

Так что теперь можно использовать следующий код:

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

Обратные вызовы аналогичны вызовов событий, разница в том, вместо того, несколько потенциальных подписчиков (например, можно связать несколько методов с `Clicked` событий или `DownloadFinished` событий) обратные вызовы могут иметь только одного подписчика.

Процесс идентичен, единственная разница, вместо предоставления имя `EventArgs` класс, который будет создан, EventArgs фактически используется для именования результирующее имя делегата C#.

Если метод в классе делегат возвращает значение, генератор привязки сопоставит это в метод делегата в родительском классе вместо события. В этих случаях необходимо предоставить значение по умолчанию, если должен возвращаться методом не подключить пользователя к делегату. Это делается с помощью [ `[DefaultValue]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) или [ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) атрибуты.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) будет жестко определить возвращаемое значение, хотя [ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) используется для указания, какой входной аргумент будет возвращаться.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Перечисления и базовые типы

Также можно ссылаться на перечисления или базовые типы, которые непосредственно не поддерживаются системой btouch определения интерфейса. Для этого поместите перечисления и основных типов в отдельный файл и включить ее в качестве одного из дополнительных файлов, предоставляемых вами btouch.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>Связывание зависимости

При связывании API, которые не являются частью приложения, необходимо убедиться, что от этих библиотек связана исполняемом файле.

Вам нужно проинформировать связывание библиотеки Xamarin.iOS, это можно сделать путем изменения конфигурации сборки для вызова `mtouch` командой некоторые дополнительные аргументы, определяющие способ подключить с помощью новой библиотеки сборки «-gcc_flags» параметр, а затем строку в кавычках, которая содержит все дополнительные библиотеки, которые требуются для вашей программе, следующим образом:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

Приведенный выше пример будет связан `libMyLibrary.a`, `libSystemLibrary.dylib` и `CFNetwork` библиотеки framework в окончательный исполняемый файл.

Или можно воспользоваться преимуществами уровня сборки [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), который можно вставить в файлах контракта (такие как `AssemblyInfo.cs`).
При использовании [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), будет необходимо иметь вашей собственной библиотеки, доступные во время внесения привязки, как это будет внедрение собственной библиотеки с помощью приложения. Пример:

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

Может возникнуть вопрос, Зачем нужна `-force_load` команды, а также причина — - ObjC флаг, несмотря на то, что он компилирует код, не сохраняет метаданные, необходимые для поддержки категорий (исключения неиспользуемого кода компоновщик или компилятор удаляет его) какие вы для Xamarin.iOS, необходимые во время выполнения.

<a name="Assisted_References" />

## <a name="assisted-references"></a>Здесь вы ссылки

Некоторые временные объекты, такие как действие листы и оповещения полях довольно сложно для отслеживания для разработчиков и генератор привязки может помочь и здесь немного.

Например, если у вас есть класс, который показал сообщение и затем создан `Done` событий, будет традиционным способом обработки такой:

```csharp
class Demo {
    MessageBox box;

    void ShowError (string msg)
    {
        box = new MessageBox (msg);
        box.Done += { box = null; ... };
    }
}
```

В сценарии выше, то разработчик должен сохранить ссылку на объект, себе и либо утечки или активно очистите ссылку для поля в свой собственный.  Хотя код привязки, генератор поддерживает отслеживания ссылки для вас и очистите его при вызове специальный метод, приведенный выше код станет:

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Обратите внимание на то, как он больше не требуется хранить переменную в экземпляре, что он работает с локальной переменной и не является необходимо, чтобы удалить ссылку, когда объект выходит из строя.

Чтобы воспользоваться преимуществами, ваш класс должен иметь значение свойства события [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) объявление, а также `KeepUntilRef` переменной присвоено имя метода, который вызывается, когда объект завершил свою работу, например Это:

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Наследование протоколов

Начиная с Xamarin.iOS версия 3.2, мы поддерживаем наследования от протоколов, которые были помечены с [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) свойство. Это полезно в определенных шаблонов API, таких как `MapKit` где `MKOverlay` протокола, наследует от `MKAnnotation` протокола и принятый ряд классов, которое унаследовано от `NSObject`.

Исторически требуется копирование протокола в каждой реализации, но в таких случаях теперь можем `MKShape` наследовать класс от `MKOverlay` протокол и он создаст все необходимые методы автоматически.

## <a name="related-links"></a>Связанные ссылки

- [Пример привязки](https://developer.xamarin.com/samples/BindingSample/)
- [Xamarin University курс: Создание библиотеку привязки Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University курс: Создание библиотеки привязки Objective-C с помощью цели Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
