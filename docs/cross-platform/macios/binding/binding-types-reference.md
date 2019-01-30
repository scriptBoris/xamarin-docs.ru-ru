---
title: Привязки типов справочное руководство
description: Это справочное руководство описывает различные атрибуты и основные понятия, необходимые для понимания при создании C# привязки для библиотек Objective-C.
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: 9c5a3cdbc8a8d5a046db90ffa48b12709359da98
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55234034"
---
# <a name="binding-types-reference-guide"></a>Привязки типов справочное руководство

В этом документе описывается список атрибутов, которые можно использовать для аннотирования файлов контракта API для привязки и создать код

Контракты Xamarin.iOS и Xamarin.Mac API создаются на языке C# в основном как определений интерфейса, которые определяют то, что код Objective-C, видимом в C#. Этот процесс включает в себя сочетание объявления интерфейса, а также некоторые базовые определения, которые могут потребовать контракта API-интерфейса. Введение в типы привязки, см. в разделе наших дополнительное руководство по [привязка библиотек Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md).

## <a name="type-definitions"></a>Определения типов

Синтаксис:

```csharp
[BaseType (typeof (BTYPE))
interface MyType [: Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Каждый интерфейс в определении контракта с [ `[BaseType]` ](#BaseTypeAttribute) атрибут, объявляющий базовый тип для создаваемого объекта. В приведенном выше объявлении `MyType` класс C# будет создан тип что привязки к типу Objective-C с именем `MyType`.

Если необходимо указать все типы после typename (в приведенном выше примере `Protocol1` и `Protocol2`) с использованием синтаксиса наследования интерфейса содержимое этих интерфейсов, будет встроенным так, как если бы они были частью контракта для `MyType`.
Способ, поверхности Xamarin.iOS, что тип использует протокол, встроив все методы и свойства, которые были объявлены в протоколе в самом типе.

Показано как объявление Objective-C `UITextField` должны быть определены в контракт Xamarin.iOS:

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

Было бы записано следующим образом как C# контракта API-интерфейса:

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

Многие другие аспекты создания кода можно управлять путем применения других атрибутов к интерфейсу, а также настройка [ `[BaseType]` ](#BaseTypeAttribute) атрибута.


### <a name="generating-events"></a>Создание событий

Является одной из особенностей разработки Xamarin.iOS и Xamarin.Mac API, что мы сопоставим Objective-C классы делегатов как C# события и обратные вызовы. Пользователи могут в каждого экземпляра, следует ли применять шаблон программирования Objective-C, путем назначения свойства, такие как `Delegate` экземпляром класса, реализующего различные методы, вызовет выполнения Objective-C, или по Выбор C#-стиля, события и свойства.

Сообщите нам см. в разделе один пример того, как использовать модель Objective-C:

```csharp
bool MakeDecision ()
{
    return true;
}

void Setup ()
{
     var scrollView = new UIScrollView (myRect);
     scrollView.Delegate = new MyScrollViewDelegate ();
     ...
}

class MyScrollViewDelegate : UIScrollViewDelegate {
    public override void Scrolled (UIScrollView scrollView)
    {
        Console.WriteLine ("Scrolled");
    }

    public override bool ShouldScrollToTop (UIScrollView scrollView)
    {
        return MakeDecision ();
    }
}
```

В приведенном выше примере видно, что мы выбрали заменить два метода, одно уведомление, что событие прокрутки вступила в месте, а второй, обратный вызов, который должен возвращать логическое значение, являющиеся инструкцией `scrollView` ли его следует перейти к первые или нет.

C# Модели пользователь библиотеки может прослушивать уведомления с помощью C# синтаксис событий или синтаксис свойств подключить обратных вызовов, которые должны возвращать значения.

Это как C# кода для той же функции выглядит как с помощью лямбда-выражения:

```csharp
void Setup ()
{
    var scrollview = new UIScrollView (myRect);
    // Event connection, use += and multiple events can be connected
    scrollView.Scrolled += (sender, eventArgs) { Console.WriteLine ("Scrolled"); }

    // Property connection, use = only a single callback can be used
    scrollView.ShouldScrollToTop = (sv) => MakeDecision ();
}
```

Так как события не возвращают значения (они имеют тип возврата void) можно подключить несколько копий. `ShouldScrollToTop` Не является событием, вместо того, он является свойством с типом `UIScrollViewCondition` которого имеет следующую сигнатуру:

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Он возвращает `bool` значение, при этом синтаксис лямбда-выражение позволяет нам просто возвращать значение из `MakeDecision` функции.

Генератор привязка поддерживает создания события и свойства, связывающие класс, как `UIScrollView` с его `UIScrollViewDelegate` (также называем их класс модели), это можно сделать, как добавлять заметки к [ `[BaseType]` ](#BaseTypeAttribute) определение с `Events` и `Delegates` параметров (см. ниже). Помимо добавления заметок к [ `[BaseType]` ](#BaseTypeAttribute) с помощью этих параметров она необходима для информирования генератор некоторые дополнительные компоненты.

Для событий, которые имеют более одного параметра (в Objective-C соглашением является первым параметром в класс делегата экземпляр объекта отправителя) необходимо указать имя, которое требуется для создаваемого `EventArgs` класса. Это делается с помощью [ `[EventArgs]` ](#EventArgsAttribute) атрибут в объявлении метода в классе модели. Пример:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

Создает указанное выше объявление `UIImagePickerImagePickedEventArgs` класс, производный от `EventArgs` и пакеты оба параметра `UIImage` и `NSDictionary`. Генератор создает это:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Затем он предоставляет следующие действия в `UIImagePickerController` класса:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

Модель методов, возвращающих значение привязываются по-разному. Это потребует как имя для создаваемого C# делегат (сигнатура для метода), а также значение по умолчанию, для возврата в случае, если пользователь не предоставил реализацию себе. Например `ShouldScrollToTop` определение — это:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

Будет создан `UIScrollViewCondition` делегата с сигнатурой, было показано выше, и если пользователь не предоставил реализацию, возвращаемое значение будет true.

В дополнение к [ `[DefaultValue]` ](#DefaultValueAttribute) атрибут, можно также использовать [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute) атрибут, который направляет генератор, чтобы вернуть значение указанного параметра в вызове или [ `[NoDefaultValue]` ](#NoDefaultValueAttribute) параметр, который указывает, что генератор что нет значения по умолчанию.

<a name="BaseTypeAttribute" />

### <a name="basetypeattribute"></a>BaseTypeAttribute

Синтаксис:

```csharp
public class BaseTypeAttribute : Attribute {
        public BaseTypeAttribute (Type t);

        // Properties
        public Type BaseType { get; set; }
        public string Name { get; set; }
        public Type [] Events { get; set; }
        public string [] Delegates { get; set; }
        public string KeepRefUntil { get; set; }
}
```

#### <a name="basetypename"></a>BaseType.Name

Использовании `Name` свойства для управления именем, этот тип будет привязан к в мире Objective-C. Обычно используется для предоставления C# введите имя, совместимый с рекомендации по разработке .NET Framework, но который сопоставляется с именем в Objective-C, который является исключением из этого соглашения.

Пример, в приведенном ниже примере мы сопоставим Objective-C `NSURLConnection` тип `NSUrlConnection`, что рекомендации по разработке .NET Framework используют «Url» вместо «URL»:

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

Указанное имя используется в качестве значения для создаваемого `[Register]` атрибут в привязке. Если `Name` не указан, тип короткое имя используется в качестве значения для `[Register]` атрибут в созданные выходные данные.

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType.Events и BaseType.Delegates

Эти свойства используются для создания C#-стиля в созданных классов событий. Они используются для связи с его классом делегата Objective-C данного класса. Возникает множество случаев, где класс использует класс делегата для отправки уведомлений и события. Например `BarcodeScanner` будет иметь дополнительное `BardodeScannerDelegate` класса. `BarcodeScanner` Класса обычно есть `Delegate` свойство, которое можно назначить экземпляр `BarcodeScannerDelegate` для, тогда как это работает, может потребоваться предоставить пользователям C#— такая как интерфейс в стиле событий, а также в таких случаях используется `Events` и `Delegates` свойства [ `[BaseType]` ](#BaseTypeAttribute) атрибута.

Эти свойства всегда равны друг с другом и должны иметь одинаковое количество элементов и синхронизироваться. `Delegates` Массив, содержащий одну строку для каждого слабо типизированной делегат, который вы хотите перенести, и `Events` массив содержит один тип для каждого типа, который необходимо связать с ним.

```csharp
[BaseType (typeof (NSObject),
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIAccelerometerDelegate)})]
public interface UIAccelerometer {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIAccelerometerDelegate {
}
```


#### <a name="basetypekeeprefuntil"></a>BaseType.KeepRefUntil

Если этот атрибут применяется, когда создаются новые экземпляры этого класса, экземпляр этого объекта будут храниться вокруг, пока метод, который ссылается `KeepRefUntil` был вызван. Это полезно для повышения удобства использования API-интерфейсов, в том случае, когда требуется запретить пользователям сохранять ссылку на объект вокруг использовать код. Значением этого свойства является имя метода в `Delegate` класса, поэтому это следует использовать в сочетании с `Events` и `Delegates` также свойства.

В следующем примере показано, как это используется `UIActionSheet` в Xamarin.iOS:

```csharp
[BaseType (typeof (NSObject), KeepRefUntil="Dismissed")]
[BaseType (typeof (UIView),
           KeepRefUntil="Dismissed",
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIActionSheetDelegate)})]
public interface UIActionSheet {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIActionSheetDelegate {
    [Export ("actionSheet:didDismissWithButtonIndex:"), EventArgs ("UIButton")]
    void Dismissed (UIActionSheet actionSheet, nint buttonIndex);
}
```


### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

Если этот атрибут применяется к определению интерфейса генератор будет невозможно создание конструктора по умолчанию.

Этот атрибут используется в том случае, когда требуется объект, который требуется инициализировать с помощью одного из других конструкторов в классе.


### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

Если этот атрибут применяется к определению интерфейса будет сообщать как закрытый конструктор по умолчанию. Это означает, что можно по-прежнему создать экземпляр объекта этого класса внутренним образом из файла расширения, но его просто не будут доступны пользователям вашего класса.

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

Этот атрибут используется в определении типа для привязки Objective-C категорий и предоставлять их как C# методы расширения для отражения способов Objective-C предоставляет функциональные возможности.

Категории представляют собой механизм Objective-C, позволяет расширить набор методов и свойств, доступных в классе.   На практике они используются для расширения функциональности базового класса (например `NSObject`) при конкретной платформы связана в последовательности (например `UIKit`), что делает их методы доступны, но только в том случае, если новая платформа связана в последовательности.   В некоторых других случаях они используются для организации функций в классе по функциональным возможностям.   Они аналогичны дух для C# методы расширения.

Это, как будет выглядеть категорию в Objective-C:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

Приведенный выше пример находится на библиотеку, которая включала бы экземпляры `UIView` с помощью метода `makeBackgroundRed`.

Чтобы привязать их, можно использовать [ `[Category]` ](#CategoryAttribute) атрибут в определении интерфейса.   При использовании [ `[Category]` ](#CategoryAttribute) атрибута, значение [ `[BaseType]` ](#BaseTypeAttribute) атрибут изменяет не могут использоваться для указания базового класса, чтобы расширить, чтобы тип для расширения.

Показано как `UIView` расширения привязки и преобразуются в C# методы расширения:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Будет создан `MyUIViewExtension` класс, содержащий `MakeBackgroundRed` метода расширения.   Это означает, что теперь можно вызвать `MakeBackgroundRed` на любом `UIView` подкласс, предоставляя те же функциональные возможности, можно получить на Objective-C.

В некоторых случаях вы найдете **статический** элементы в категории, такие как в следующем примере:

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

Это приведет к **неправильный** категории C# определение интерфейса:

```csharp
[Category]
[BaseType (typeof (FooObject))]
interface FooObject_Extensions {

    // Incorrect Interface definition
    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Это неверно так как для использования `BoolMethod` расширения требуется экземпляр `FooObject` , но связывании ObjC **статический** расширения, это является побочным эффектом, из-за того, как C# реализуются методы расширения .

Единственный способ использовать приведенные выше определения — следующий сложный код:

```csharp
(null as FooObject).BoolMethod (range);
```

Во избежание этого рекомендуется встроенный `BoolMethod` определение внутрь `FooObject` само определение интерфейса, это позволит вызывать это расширение, как она предназначена `FooObject.BoolMethod (range)`.

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Мы выдаст предупреждение (BI1117), каждый раз, когда нам найти [ `[Static]` ](#StaticAttribute) члена внутри [ `[Category]` ](#CategoryAttribute) определения. Если Вы действительно хотите иметь [ `[Static]` ](#StaticAttribute) члены внутри вашей [ `[Category]` ](#CategoryAttribute) определений, предупреждение можно отключить с помощью `[Category (allowStaticMembers: true)]` или снабжения иличлен[ `[Category]` ](#CategoryAttribute) определение с помощью интерфейса [ `[Internal]` ](#InternalAttribute).

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

Если этот атрибут применяется к классу он будут лишь генерировать статический класс, который является производным от `NSObject`, поэтому [ `[BaseType]` ](#BaseTypeAttribute) атрибут учитывается. Статические классы используются для размещения C общих переменных, которые вы хотите предоставить.

Пример:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Создаст C# класса с помощью следующего API:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>Протокол/модель определения

Модели обычно используются реализацией протокола.
Они отличаются тем, что среда выполнения будет регистрировать только с Objective-C методы, которые фактически были перезаписаны.
В противном случае метод не будут регистрироваться.

В целом это означает, что при подкласс класс, помеченный с `ModelAttribute`, не следует вызывать базовый метод.   Вызов этого метода приведет к возникновению исключения, вы должны реализовать весь поведение в подклассе, для любых методов, которые можно переопределить.

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

По умолчанию члены, которые являются частью протокола не являются обязательными. Это позволяет пользователям создавать подкласс `Model` объекта путем просто наследования от класса C# и переопределение только те методы, которые их интересуют. Иногда требует контракт Objective-C, что пользователь предоставляет реализацию этого метода (с тем, описываются `@required` директив в Objective-C). В таком случае вы помечаете методы с `[Abstract]` атрибута.

`[Abstract]` Атрибут может применяться к методам и свойствам и приводит к флаг созданный элемент как абстрактный и класс, который требуется абстрактный класс генератора.

Следующий фрагмент взят из Xamarin.iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UITableViewDataSource {
    [Export ("tableView:numberOfRowsInSection:")]
    [Abstract]
    nint RowsInSection (UITableView tableView, nint section);
}
```

<a name="DefaultValueAttribute" />

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

Указывает значение по умолчанию, возвращаемое методом модели, если пользователь не предоставляет метод, который данный конкретный метод в объекте модели

Синтаксис:

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

Например, в следующем классе мнимой делегат для `Camera` класса, мы предоставляем `ShouldUploadToServer` которого будет отображаться в качестве свойства на `Camera` класса. Если пользователь `Camera` класс не задано явно значение лямбда-выражению, которая может реагировать true или false, возвращаемое значение по умолчанию в данном случае было бы значение равно false, значение, которое мы указали в `DefaultValue` атрибут:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

Если пользователь задает обработчик в классе мнимой, это значение будет игнорироваться:

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

См. также: [ `[NoDefaultValue]` ](#NoDefaultValueAttribute), [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute).

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

Синтаксис:

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

Этот атрибут, если для метода, который возвращает значение на класс модели проинструктирует генератор возвращаемое значение указанного параметра в том случае, если пользователь не предоставил свой собственный метод или лямбда-выражения.

Пример

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

В приведенном выше случае пользователь `NSAnimation` класс решили использовать любой из C# событий и свойств и не задал `NSAnimation.ComputeAnimationCurve` метода или лямбда-выражение, возвращаемое значение будет значение, передаваемое в параметре хода выполнения.

См. также: [ `[NoDefaultValue]` ](#NoDefaultValueAttribute), [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

Иногда смысл не предоставляется доступ к событию или делегата свойство из класса модели в класс узла, поэтому при добавлении этого атрибута проинструктирует генератор, чтобы избежать создания любого метода, с его.

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);

    [Export ("imagePickerController:didFinishPickingImage:"), IgnoredInDelegate)] // No event generated for this method
    void FinishedPickingImage (UIImagePickerController picker, UIImage image);
}
```

### <a name="delegatenameattribute"></a>DelegateNameAttribute

Этот атрибут используется в модели методы, возвращающие значения, чтобы задать имя сигнатуру делегата для использования.

Пример

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

В приведенном выше определении генератор выдаст следующее объявление открытого:

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

Этот атрибут используется, чтобы разрешить генератор для изменения имени свойства, создаваемого в классе ведущего. Иногда полезно при имя метода класса FooDelegate лучше всего подходит для класса делегата, но будет выглядеть нечетное в классе ведущего как свойство.

Кроме того, это действительно полезно (и необходимые) Если у вас есть два или дополнительные перегрузки методов, которые имеет смысл сохранить их с именем, так как находится в классе FooDelegate, но вы хотите предоставить их в классе ведущего с заданным именем, лучше.

Пример

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

В приведенном выше определении генератор выдаст следующее объявление открытый в классе ведущего:

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

Для событий, которые имеют более одного параметра (в Objective-C соглашением является первым параметром в класс делегата экземпляр объекта отправителя) необходимо указать имя, которое требуется для создаваемого класса EventArgs может иметь. Это делается с помощью `[EventArgs]` атрибут в объявлении метода в вашей `Model` класса.

Пример:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

Создает указанное выше объявление `UIImagePickerImagePickedEventArgs` класс, производный от EventArgs, пакеты оба параметра `UIImage` и `NSDictionary`. Генератор создает это:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Затем он предоставляет следующие действия в `UIImagePickerController` класса:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```


### <a name="eventnameattribute"></a>EventNameAttribute

Этот атрибут используется, чтобы разрешить генератор изменить имя события или свойства в классе. Иногда полезно при имя метода класса модели лучше всего подходит для класс модели, но могли бы выглядеть нечетное в исходного класса события или свойства.

Например `UIWebView` использует следующий бит из `UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

Выше предоставляет `LoadingFinished` как метод в `UIWebViewDelegate`, но `LoadFinished` как событие может подключить до `UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

При применении `[Model]` атрибута в определение типа в контракте API, среда выполнения создаст специальный код, который будут исследовать только вызовы к методам в классе, если пользователю была записана поверх метода в классе. Этот атрибут обычно применяется ко всем API, которые являются оболочкой класс делегата Objective-C.

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

Указывает, что возвращаемое значение по умолчанию не поддерживает метод на модели.

Это работает в среде выполнения Objective-C, отвечая `false` в запросе среды выполнения Objective-C, чтобы определить, если указанному селектору реализуется в этом классе.

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

См. также: [ `[DefaultValue]` ](#DefaultValueAttribute), [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>Протоколы

Концепция протокола Objective-C не действительно существует в C#. Протоколы, аналогичны C# интерфейсов, но они отличаются тем, что не все методы и свойства, объявленные в протоколе, должны быть реализованы классом, который принимает его. Вместо этого некоторые методы и свойства являются необязательными.

Некоторые протоколы обычно используются в качестве классов модели, те должны быть привязаны с помощью [ `[Model]` ](#ModelAttribute) атрибута.

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
```

Начиная с версии 7.0 Xamarin.iOS протокол новых и улучшенных возможностей привязки был включен.  Любое определение, которое содержит `[Protocol]` атрибут на самом деле создаст три вспомогательных классов, которые значительно расширяют способ предоставления протоколы:

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

**Реализацию класса** предоставляет полный абстрактный класс, можно переопределить отдельные методы и получить полный тип безопасности. Но из-за C# не поддерживает множественное наследование, существуют сценарии, где вы требуют другой базовый класс, но по-прежнему необходимо реализовать интерфейс.

Именно здесь созданный **определение интерфейса** пригодиться.  Это интерфейс, который содержит все необходимые методы от протокола.  Это позволяет разработчикам, которые необходимо реализовать протокол просто реализовать интерфейс.  Среда выполнения автоматически зарегистрирует тип как внедрение протокола.

Обратите внимание на то, что интерфейс только перечислены необходимые методы и предоставлять необязательные методы.   Это означает, что классы, применяющие протокол получите полную проверку типов для требуемых методов, но придется прибегнуть к слабой типизацией (вручную с помощью атрибутов экспорта и соответствия сигнатуры) для методов необязательный протокол.

Для удобства использования API, который использует протоколы средство привязки также создаст класс метода расширения, который предоставляет все необязательные методы.   Это означает, что до тех пор, пока для использования API, можно рассматривать как имеющий все методы протоколы.

Если вы хотите использовать определения протокола в API, необходимо написать скелет пустых интерфейсов в определении API.   Если вы хотите использовать MyProtocol в интерфейсе API, потребуется это сделать:

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

### <a name="adopting-protocol-generated-interfaces"></a>Внедрение сформированные протокола интерфейсы

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

### <a name="protocol-inlining"></a>Встраивание протокола

При связывании существующих типов Objective-C, которые были объявлены как внедрение протокола требуется встроенный протокол напрямую. Чтобы сделать это, просто объявите протокол как интерфейс без каких-либо [ `[BaseType]` ](#BaseTypeAttribute) атрибута и список протокола в список базовых интерфейсов для интерфейса.

Пример

```csharp
interface SpeakProtocol {
    [Export ("say:")]
    void Say (string msg);
}

[BaseType (typeof (NSObject))]
interface Robot : SpeakProtocol {
    [Export ("awake")]
    bool Awake { get; set; }
}
```


## <a name="member-definitions"></a>Определения элементов

Атрибуты этого раздела применяются к отдельным членам типа: свойства и объявления метода.


### <a name="alignattribute"></a>AlignAttribute

Можно задать значение выравнивания для возврата типов свойств. Некоторые свойства принимают указатели адресов, которые должны быть выровнены в определенные границы (в Xamarin.iOS, это происходит, например, с некоторыми `GLKBaseEffect` свойства, которые должны быть 16-байтовое выравнивание). Это свойство используется для оформления метод считывания и использовать значение выравнивания. Обычно используется с `OpenTK.Vector4` и `OpenTK.Matrix4` типов при интеграции с Objective-C API-интерфейсы.

Пример

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```


### <a name="appearanceattribute"></a>AppearanceAttribute

`[Appearance]` Атрибут ограничена iOS 5, где был введен manager внешний вид.

`[Appearance]` Атрибут может применяться к любой метод или свойство, которое участвовать в `UIAppearance` framework. Когда этот атрибут применяется к методу или свойству в классе, он будет направлять генератора привязки для создания класса со строгой типизацией внешний вид, используемого для стиля все экземпляры этого класса или экземпляров, которые удовлетворяют определенным условиям.

Пример

```csharp
public interface UIToolbar {
    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);
}
```

Выше приведет к возникновению следующий код в UIToolbar:

```csharp
public partial class UIToolbar {
    public partial class UIToolbarAppearance : UIView.UIViewAppearance {
        public virtual void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);
        public virtual UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics)
    }
    public static new UIToolbarAppearance Appearance { get; }
    public static new UIToolbarAppearance AppearanceWhenContainedIn (params Type [] containers);
}
```

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (Xamarin.iOS 5.4)

Используйте `[AutoReleaseAttribute]` на методы и свойства, чтобы заключить в нее вызов метода в метод `NSAutoReleasePool`.

В Objective-C есть некоторые методы, возвращающие значения, которые добавляются по умолчанию `NSAutoReleasePool`. По умолчанию, они бы перейти на ваш вопрос `NSAutoReleasePool`, но так как Xamarin.iOS также хранит ссылки на объекты, до тех пор, пока существует управляемый объект, не может потребоваться сохранить ссылку на дополнительные `NSAutoReleasePool` которой только получить останавливаются до потока Возвращает элементов управления до следующего потока, или вернуться к основной цикл.

Этот атрибут применяется к примеру в условиях большой свойства (например `UIImage.FromFile`), возвращает объекты, которые были добавлены по умолчанию `NSAutoReleasePool`. Без этого атрибута изображения будет храниться до тех пор, пока поток не возвратил управления основного цикла. Uf потока был какую-либо фонового загрузчика, который всегда активен и ожидающая работу, образы никогда не снимается.

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`[ForcedTypeAttribute]` Используется для принудительного применения создания управляемого типа, даже если неуправляемый возвращаемый объект не соответствует типу, описанных в определении привязки.

Это полезно, когда тип, описанный в заголовок соответствует возвращаемого типа этого метода, например, возьмем следующее определение Objective-C из `NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

Явно указано, что он будет возвращать `NSURLSessionDownloadTask` экземпляра, но еще он **возвращает** `NSURLSessionTask`, который является суперклассом и таким образом, не преобразуется в `NSURLSessionDownloadTask`. Так как мы находимся в контексте типобезопасный `InvalidCastException` произойдет.

В соответствии с описанием заголовка и избежать `InvalidCastException`, `[ForcedTypeAttribute]` используется.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

`[ForcedTypeAttribute]` Также принимает логическое значение с именем `Owns` то есть `false` по умолчанию `[ForcedType (owns: true)]`. Владеет параметр используется для отслеживания [политикой владения](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) для **основу** объектов.

`[ForcedTypeAttribute]` Допустим только для параметров, свойства и возвращаемое значение.

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

`[BindAsAttribute]` Допускает привязку `NSNumber`, `NSValue` и `NSString`(перечисления) в более точные C# типов. Атрибут может использоваться для создания лучшую, более точным, API-интерфейса .NET через собственные API.

Вы можете дополнить методы (возвращаемое значение), параметры и свойства с `BindAs`. Единственным ограничением является то, что член **не должны** находиться внутри `[Protocol]` или [ `[Model]` ](#ModelAttribute) интерфейс.

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

Текущие типы поддерживаемых инкапсуляция являются:

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

Следующие C# поддерживаются типы данных, который необходимо инкапсулировать в или из `NSValue`:

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint / PointF
* CGRect / RectangleF
* CGSize / SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

Следующие C# поддерживаются типы данных, который необходимо инкапсулировать в или из `NSNumber`:

* bool
* byte
* double
* float
* short
* int
* long
* sbyte
* ushort
* uint
* ulong
* nfloat
* nint
* nuint
* перечислениям;

#### <a name="nsstring"></a>NSString

[`[BindAs]`](#BindAsAttribute) работает в conjuntion с [перечислений, поддерживаемый константа NSString](#enum-attributes) , поэтому можно создавать улучшенный интерфейс API .NET, например:

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

Пример кода выведет данные:

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

Будет осуществлен `enum`  <->  `NSString` преобразования только в том случае, если тип предоставленного перечисления [ `[BindAs]` ](#BindAsAttribute) — [с константой NSString](#enum-attributes).

#### <a name="arrays"></a>Массивы

[`[BindAs]`](#BindAsAttribute) также поддерживает массивы любого из поддерживаемых типов, можно задать следующее определение API в качестве примера:

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

Пример кода выведет данные:

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

`rects` Инкапсулируется в `NSArray` , содержащий `NSValue` для каждого `CGRect` в ответ вы получите массив `CAScroll?` которого будет создана с помощью значения, возвращаемого `NSArray` содержащий `NSStrings`.

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

`[Bind]` Атрибут имеет два применения один при применении к методу или объявление свойства и еще один, при применении к отдельным доступа get или SET в свойстве.

При использовании метода или свойства, эффект `[Bind]` атрибута является создание метода, который вызывает указанному селектору. Но не снабжен итоговый созданный метод [ `[Export]` ](#ExportAttribute) атрибута, это означает, что он не может участвовать в переопределении метода. Обычно используется в сочетании с `[Target]` атрибут для реализации методов расширения Objective-C.

Пример:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

При использовании в метод получения или задания, `[Bind]` атрибут используется для изменения значения по умолчанию определены генератором кода при создании имен селектор Objective-C методы получения и задания для свойства. По умолчанию, когда вы помечаете свойство с именем `fooBar`, генератору следует формировать `fooBar` экспорта для метода получения и `setFooBar:` для метода задания. В некоторых случаях Objective-C не следуют соглашению, обычно они измените имя метода получения быть `isFooBar`.
Этот атрибут используется для информирования этого генератора.

Пример:

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

<a name="AsyncAttribute" />

### <a name="asyncattribute"></a>AsyncAttribute

Только на Xamarin.iOS 6.3 и более поздних.

Этот атрибут может применяться к методам, которые принимают обработчик завершения, как их последнего аргумента.

Можно использовать `[Async]` атрибут в методах, чьи последним аргументом является обратным вызовом.  Когда вы примените к методу, привязки генератору формировать версии этого метода с суффиксом `Async`.  Если обратный вызов не принимает никаких параметров, возвращаемое значение будет `Task`, если обратный вызов принимает параметр, результат будет `Task<T>`.

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

Следующие создаст этот асинхронный метод:

```csharp
Task LoadFileAsync (string file);
```

Если обратный вызов принимает несколько параметров, следует задать `ResultType` или `ResultTypeName` указать желаемое имя созданного типа, которая будет содержать все свойства.

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

Следующие создаст в асинхронном методе, где `FileLoading` содержит свойства для доступа как к `files` и `byteCount`:

```csharp
Task<FileLoading> LoadFile (string file);
```

Если последний параметр обратного вызова является `NSError`, созданный `Async` метод будет проверять, если значение не равно null, и если это так, созданный асинхронный метод установит исключения задачи.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

Оно создает следующий асинхронный метод:

```csharp
Task<string> UploadAsync (string file);
```

И в случае ошибки результирующая задача будет иметь значение исключение `NSErrorException` , который служит оболочкой итоговый `NSError`.

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

Это свойство используется для указания значения для возвращения `Task` объекта.   Этот параметр принимает значение существующего типа, поэтому должна быть определены в одной из основных определений api.

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

Это свойство используется для указания значения для возвращения `Task` объекта.   Этот параметр принимает имя вашей требуемое название типа, генератор создает ряд свойств, по одному для каждого параметра, который принимает обратного вызова.

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

Это свойство можно используйте для настройки имени создаваемого асинхронного метода.   По умолчанию используется имя метода и добавьте текст «Async», это можно использовать, чтобы изменить это значение по умолчанию.

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

Этот атрибут применяется к строковых параметров или свойств строки и указывает, что генератор кода не маршалинг строк копирования для этого параметра, а вместо этого создайте новый экземпляр NSString из C# строка.
Этот атрибут необходим только для строк, если вы указываете генератор для использования, маршалинг строки копирования с помощью `--zero-copy` параметр командной строки или атрибут уровня сборки `ZeroCopyStringsAttribute`.

Это происходит в случаях, где объявлено данное свойство в Objective-C, чтобы быть `retain` или `assign` вместо свойства `copy` свойство. Они обычно происходят в сторонних библиотек, которые неправильно «оптимизированы» разработчиками. В общем случае `retain` или `assign` `NSString` с момента неверно определены свойства, `NSMutableString` или классы, производные от пользователя из `NSString` может привести к изменению содержимого строк независимо от кода библиотеки, может привести к некоторой критические приложение. Обычно это происходит из-за Преждевременная оптимизация.

Ниже показан два свойства в Objective-C:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```


### <a name="disposeattribute"></a>DisposeAttribute

При применении `[DisposeAttribute]` к классу, предоставляют фрагмент кода, который будет добавлен к `Dispose()` реализацию метода класса.

Так как `Dispose` метод создается автоматически `bmac-native` и `btouch-native` средства, необходимо использовать `[Dispose]` атрибут внедрить код в созданный `Dispose` реализацию метода.

Пример:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

`[Export]` Атрибут используется для пометки метода или свойства в среде выполнения Objective-C. Этот атрибут общим средство привязки и фактическое сред выполнения Xamarin.iOS и Xamarin.Mac. Для методов, этот параметр передается дословно в сформированный код для свойства, задания и считывания экспортов, создаются на базовом объявлении основе (см. в разделе [ `[BindAttribute]` ](#BindAttribute) сведения о том, как изменить поведение средство привязки).

Синтаксис:

```csharp
public enum ArgumentSemantic {
    None, Assign, Copy, Retain.
}

[AttributeUsage (AttributeTargets.Method | AttributeTargets.Constructor | AttributeTargets.Property)]
public class ExportAttribute : Attribute {
    public ExportAttribute();
    public ExportAttribute (string selector);
    public ExportAttribute (string selector, ArgumentSemantic semantic);
    public string Selector { get; set; }
    public ArgumentSemantic ArgumentSemantic { get; set; }
}
```

[Селектор](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html) представляет имя базовой Objective-C метод или свойство, к которому осуществляется привязка.

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

Этот атрибут используется для предоставления глобальной переменной C как поле, которое загружается по запросу и подвержен C# кода. Обычно это необходимо для получения значения констант, которые определены в C или Objective-C, может быть либо токены, используемые в некоторые интерфейсы API и, значения которых не видны и должен использоваться в качестве — является кодом пользователя.

Синтаксис:

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName` Является символ C связь с файлом. По умолчанию это будут загружены из библиотеки, имя которого выводится из пространства имен, где определен тип. Если это не библиотеки, где выполняется поиск символа, следует передать `libraryName` параметра. При связывании статической библиотеки, используйте `__Internal` как `libraryName` параметр.

Создаваемые свойства всегда являются статическими.

Свойства, помеченной этим атрибутом поля могут быть следующих типов:

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* перечислениям;

Методы задания не поддерживаются для [перечислений, поддерживаемый константы NSString](#enum-attributes), но они могут быть вручную привязаны при необходимости.

Пример

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute" />

### <a name="internalattribute"></a>InternalAttribute

`[Internal]` Атрибут может применяться к методам и свойствам и вызывает пометки созданный код с `internal` C# ключевое слово, что делает код только доступными для кода в созданную сборку. Обычно используется для скрытия API, которые являются слишком низкого уровня или предоставить неоптимальный открытый API, который вы хотите улучшить после ее или для API-интерфейсов, которые не поддерживаются генератором и требуют некоторых кодировать вручную.

При создании привязки обычно бы скрыть метод или свойство, с помощью этого атрибута и укажите другое имя для метода или свойства, а затем на вашей C# файл взаимодополняющие поддержки, необходимо добавить оболочку со строгой типизацией, которая предоставляет Базовая функциональность.

Пример:

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

Затем в файле вспомогательные имеется некоторый код следующим образом:

```csharp
public NSObject this [NSObject idx] {
    get {
        return _GetValueForKey (idx);
    }
    set {
        _SetValueForKey (value, idx);
    }
}
```

<a name="IsThreadStaticAttribute" />

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

Этот атрибут помечает резервное поле для свойства, для которого создается заметка с помощью .NET `[ThreadStatic]` атрибута. Это полезно в том случае, если поле является статической переменной потока.

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin.iOS 6.0.6)

Этот атрибут будет делать исключения метод поддержки собственного (Objective-C).
Вместо вызова метода `objc_msgSend` напрямую, вызов проходит через пользовательские trampoline, который перехватывает исключения ObjectiveC и маршалирует их в управляемые исключения.

В настоящее время лишь немногие `objc_msgSend` подписи поддерживаются (вы узнаете, если подпись не поддерживается при связывании собственного приложения, которое использует эту привязку с помощью отсутствует monotouch_*_objc_msgSend* символов), но более может быть добавить в запрос.


### <a name="newattribute"></a>NewAttribute

Этот атрибут применяется к методам и создать свойства, чтобы генератор `new` ключевое слово перед объявлением.

Он позволяет избежать предупреждений компилятора, если имя свойства или же метод впервые появился в подкласс, в котором уже существует в базовом классе.

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

Этот атрибут можно применять к поля могут иметь создают Генератор строго типизированный вспомогательный класс уведомлений.

Этот атрибут может использоваться без аргументов для уведомлений, которые содержат полезные данные не найдены, или можно указать `System.Type` , ссылающийся на другой интерфейс в определении API, обычно с именем, заканчивающимся «EventArgs». Генератор приводит к отключению интерфейса в класс, который наследуется от класса `EventArgs` и будет включать все свойства в списке. [ `[Export]` ](#ExportAttribute) Атрибут должен использоваться в `EventArgs` класса, чтобы получить список имя ключа, используемый для поиска словаря Objective-C, чтобы получить значение.

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
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Пользователи кода можно затем легко подписаться на уведомления отправляется [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) с помощью следующего кода:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

Или для задания определенного объекта, который следует контролировать. Если передать `null` для `objectToObserve` этот метод будет вести себя так же, как его однорангового узла.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

Значение, возвращаемое `ObserveDidStart` может использоваться в легко прекратить получение уведомлений, следующим образом:

```csharp
token.Dispose ();
```

Или можно вызвать [NSNotification.DefaultCenter.RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) и передать маркер. Уведомление содержит параметры, следует указать вспомогательный объект `EventArgs` интерфейса следующим образом:

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

Выше создаст `MyScreenChangedEventArgs` класса `ScreenX` и `ScreenY` свойства, которые получает данные из [NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo) словарь с использованием ключей `ScreenXKey` и `ScreenYKey` соответственно и применить соответствующие преобразования. `[ProbePresence]` Атрибут используется для генератора для выборки данных, если задано значение ключа `UserInfo`, вместо того, чтобы извлечь значение. Используется для случаев, когда наличие ключа является значение (обычно для логических значений).

Это позволяет писать код следующим образом:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

В некоторых случаях нет константу, не связанные со значением, передаваемым в словаре.  Apple иногда используются константы открытых символов и иногда использует строковых констант.  По умолчанию [ `[Export]` ](#ExportAttribute) атрибут в ваш предоставленный `EventArgs` класс будет использовать указанное имя в качестве общедоступных символов выполняется поиск во время выполнения.  Если это не так, а вместо этого он должен выполняться поиск как строковую константу, а затем передавать `ArgumentSemantic.Assign` значение атрибут Export.

**Новые возможности Xamarin.iOS 8.4**

В некоторых случаях уведомления начнется жизнь без аргументов, поэтому использование [ `[Notification]` ](#NotificationAttribute) без аргументов является приемлемым.  Но в некоторых случаях будут вводиться параметры уведомления.  Для поддержки этого сценария, атрибут может применяться несколько раз.

Если вы разрабатываете привязку, и чтобы не разрушать существующий код пользователя, был годен существующее уведомление из:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

В версию, содержащая список уведомлений атрибут дважды, следующим образом:

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

<a name="NullAllowedAttribute" />

### <a name="nullallowedattribute"></a>NullAllowedAttribute

Когда это применяется к свойству, он помечает свойство как допускающий значение `null` для ей назначен. Это допустимо только для ссылочных типов.

Если это применяется к параметра в сигнатуре метода, он указывает, что указанный параметр может иметь значение null, и что проверка должна быть выполнена для передачи `null` значения.

Если этот атрибут не имеет ссылочный тип, средство привязки создаст проверяет наличие значения, присваиваемого перед их передачей Objective-C и создаст проверку, что вызовет `ArgumentNullException` Если значение, присвоенное `null`.

Пример:

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute" />

### <a name="overrideattribute"></a>OverrideAttribute

Этот атрибут используется для указания привязки генератор, что привязка для этого конкретного метода должны быть отмечены с `override` ключевое слово.

### <a name="presnippetattribute"></a>PreSnippetAttribute

Этот атрибут можно использовать для вставки кода, вставляемый после проверки входных параметров, но до вызова кода в Objective-C.

Пример

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

Этот атрибут можно использовать для вставки кода, вставляемый перед любой параметр проверяются в созданный метод.

Пример

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

Указывает, что генератор привязки для вызова заданного свойства из этого класса для извлечения значения из него.

Это свойство обычно используется для обновления кэша, указывающий ссылаться на объекты, для которых сохраняются ссылки на графе объектов. Обычно оно отображается в коде, который содержит такие операции, как добавить или удалить. Этот метод используется, чтобы после элементов добавляются или удаляются, что внутренний кэш обновляться, чтобы убедиться, что мы используем самые управляемые ссылки на объекты, которые действительно используются. Это возможно, поскольку средство привязки создает резервное поле для всех ссылок объектов в данной привязки.

Пример

```csharp
[BaseType (typeof (NSObject))]
[Since (4,0)]
public interface NSOperation {
    [Export ("addDependency:")][PostGet ("Dependencies")]
    void AddDependency (NSOperation op);

    [Export ("removeDependency:")][PostGet ("Dependencies")]
    void RemoveDependency (NSOperation op);

    [Export ("dependencies")]
    NSOperation [] Dependencies { get; }
}
```

В этом случае `Dependencies` свойство будет вызываться после добавления или удаления зависимости из `NSOperation` объекта, чтобы получился граф, который представляет фактический загружены объекты, предотвращение утечек памяти как повреждение памяти.

### <a name="postsnippetattribute"></a>PostSnippetAttribute

Этот атрибут можно использовать для вставки некоторые C# исходного кода, вставляемый после, как код вызывает базовый метод Objective-C

Пример

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

Этот атрибут применяется к возвращаемым значениям, чтобы пометить их как прокси-объектов. Некоторые интерфейсы API Objective-C возвращаемое прокси-объектов, которые не могут отличаться от пользователя привязки. Этот атрибут применяется для пометки объектов как `DirectBinding` объекта. Для сценария в Xamarin.Mac, вы увидите [обсуждение на эту ошибку](https://bugzilla.novell.com/show_bug.cgi?id=670844).

### <a name="retainlistattribute"></a>RetainListAttribute

Указывает, что генератор управляемый Справочник по параметру сохранить или удалить внутреннюю ссылку на параметр. Это позволяет сохранить ссылки на объекты.

Синтаксис:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Если значение `doAdd` имеет значение true, то добавляется параметр `__mt_{0}_var List<NSObject>;`. Где `{0}` заменяется заданного `listName`. В разделяемом классе взаимодополняющие к API необходимо объявить этот резервное поле.

Пример см. в разделе [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) и [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (Xamarin.iOS 6.0)

Это может применяться для возврата типов, чтобы указать, что генератор должен вызывать `Release` объекта перед его возвратом. Это требуется, только когда метод передает объект сохранено (в отличие от autoreleased объект, который является наиболее распространенным сценарием)

Пример

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

Кроме того этот атрибут распространяется на созданного кода, таким образом, чтобы среда выполнения Xamarin.iOS знает, что его необходимо сохранить объект при возвращении Objective-c с такой функции.


### <a name="sealedattribute"></a>SealedAttribute

Указывает, что генератор для пометки созданный метод как запечатанный. Если этот атрибут не указан, по умолчанию используется для создания виртуального метода (виртуальный метод, абстрактный метод или переопределения в зависимости от того, как используются другие атрибуты).

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

Когда `[Static]` атрибут применяется к методу или свойству, это приводит к возникновению ошибки статический метод или свойство. Если этот атрибут не указан, генератор создает метода экземпляра или свойства.


### <a name="transientattribute"></a>TransientAttribute

Используйте этот атрибут для свойства флагов, значения которого являются временными, то есть объекты, которые создаются временно по iOS, но не имеют большой срок действия. Когда этот атрибут применяется к свойству, генератор не приводит к созданию резервное поле для этого свойства, это означает, что управляемый класс не сохраняет ссылку на объект.

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

В конструкторе Xamarin.iOS/Xamarin.Mac привязок `[Wrap]` атрибут используется для создания оболочки слабо типизированный объект с объектом со строгой типизацией. Это вступает в действие преимущественно с Objective-C объекты делегатов, которые обычно объявлены как относящиеся к типу `id` или `NSObject`. Является правилом, используемым Xamarin.iOS и Xamarin.Mac для предоставления этих делегатов или источники данных как относящиеся к типу `NSObject` и именуются по соглашению «Weak» + имя раскрытия. `id delegate` Свойства из Objective-C будет отображаться в качестве `NSObject WeakDelegate { get; set; }` свойства в файле контракта API.

Но обычно значение, которое назначается этот делегат вызывается строгий тип, поэтому мы поверхность строгий тип и применить `[Wrap]` атрибута, это означает, что пользователи могут использовать слабые типы, если они нужны некоторые fine-элемент управления или если им необходимо прибегнуть к tric низкого уровня KS, или же их можно использовать свойство со строгой типизацией для большую часть своей работы.

Пример

```csharp
[BaseType (typeof (NSObject))]
interface Demo {
     [Export ("delegate"), NullAllowed]
     NSObject WeakDelegate { get; set; }

     [Wrap ("WeakDelegate")]
     DemoDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
interface DemoDelegate {
    [Export ("doDemo")]
    void DoDemo ();
}
```

Это, как пользователь будет использовать версии слабо типизированной делегата:

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

И это, как пользователь будет использовать версию со строгой типизацией, обратите внимание на то, что пользователь использует преимущества C#и в системе типов с помощью ключевого слова переопределения, чтобы объявить свои намерения и что он не нужно вручную примените к методу `[Export]`, так как мы сделали эту работу в привязке для пользователя:

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

Другим использованием `[Wrap]` атрибут предназначен для поддержки версии методы со строгой типизацией.  Пример:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Элементы, формируемые службами `[Wrap]` не `virtual` по умолчанию, если вам нужна `virtual` можно задать для члена `true` необязательный `isVirtual` параметра.

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

## <a name="parameter-attributes"></a>Атрибуты параметра

В этом разделе описываются атрибуты, применяемые к параметрам в определение метода, а также `[NullAttribute]` , применимый к свойству в целом.

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

Этот атрибут применяется к типам параметров в C# объявлений делегатов для уведомления средство привязки, соответствует Objective-C в соответствующий параметр Блокировать соглашение о вызовах и следует маршалировать его таким образом.

Обычно используется для обратных вызовов, которые определены следующим образом в Objective-C:

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

См. также: [CCallback](#CCallback).

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

Этот атрибут применяется к типам параметров в C# объявления для уведомления связыватель, что в соответствующий параметр соответствует указатель на функцию C ABI, соглашение о вызовах и следует маршалировать его таким образом делегатов.

Обычно используется для обратных вызовов, которые определены следующим образом в Objective-C:

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

См. также: [BlockCallback](#BlockCallback).

### <a name="params"></a>params

Можно использовать `[Params]` атрибут для последнего параметра массива определению метода, чтобы внедрить «params» в определении генератор.   Это позволяет привязку к простому разрешению для необязательных параметров.

Например следующее определение:

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

Позволяет следующий код для записи:

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

Это имеет дополнительное преимущество, что он не требуется для создания массива исключительно для передачи элементов.

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

Можно использовать `[PlainString]` атрибут перед параметров строки для указания привязки генератор передать строку как строку C, вместо того чтобы передавать параметр как `NSString`.

Большинство интерфейсов API Objective-C используют `NSString` параметры, но небольшое число API-интерфейсы предоставляют `char *` API для передачи строк, а не `NSString` вариантов.
Используйте `[PlainString]` в таких случаях.

Например, следующие объявления Objective-C:

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

Должен быть привязан к следующим образом:

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

Указывает, что генератор, чтобы хранить ссылку на указанный параметр. Генератор предоставит резервного хранилища для этого поля, или можно указать имя ( `WrapName`) для хранения значения в. Это полезно для хранения ссылки на управляемый объект, который передается как параметр Objective-C, и если известно, что Objective-C будут сохранены только эта копия объекта. Например, API, например `SetDisplay (SomeObject)` этот атрибут будет использовать как вполне вероятно, что SetDisplay только может отображать один объект за раз. Если требуется для отслеживания более одного объекта (например, для API стека по принципу) используется `[RetainList]` атрибута.

Синтаксис:

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```


### <a name="retainlistattribute"></a>RetainListAttribute

Указывает, что генератор управляемый Справочник по параметру сохранить или удалить внутреннюю ссылку на параметр. Это позволяет сохранить ссылки на объекты.

Синтаксис:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Если значение `doAdd` имеет значение true, то добавляется параметр `__mt_{0}_var List<NSObject>`. Где `{0}` заменяется заданного `listName`. В разделяемом классе взаимодополняющие к API необходимо объявить этот резервное поле.

Пример см. в разделе [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) и [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)


### <a name="transientattribute"></a>TransientAttribute

Этот атрибут применяется к параметрам и используется только в том случае, если переход от Objective-C, чтобы C#.  Во время таких переходов различных Objective-C `NSObject` параметры упаковываются в управляемое представление объекта.

Среда выполнения принимает ссылку на исходный объект и сохранять ссылку на до последнего управляемый Справочник по объекту отсутствует и сборщике Мусора также предусмотрена возможность запуска.

В некоторых случаях важно C# среды выполнения, чтобы не сохранять ссылку на собственный объект.  Это иногда происходит прикрепление базовый собственный код специальное поведение к жизненному циклу параметра.  Например: деструктор для параметра выполнения некоторых операций очистки или удаления некоторых ценным ресурсом.

Этот атрибут сообщает среде выполнения, как нужно объект удален, если это возможно при возврате к Objective-C из своего метода перезаписан.

Простое правило: Если среда выполнения была возможность создания новых управляемое представление собственного объекта, то в конце функции, хранить счетчик для собственного объекта будут удалены и свойство дескриптора управляемого объекта будет очищено.   Это означает, что если вы сохранил ссылку на управляемый объект, такая ссылка станет бесполезен (вызов методов на нем будет выдано исключение).

Если переданный объект не был создан или был уже необработанных управляемых представлением объекта, принудительное dispose не выполняется. 


## <a name="property-attributes"></a>Атрибуты свойства

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

Этот атрибут используется для поддержки идиома Objective-C, где свойство с получения впервые появился в базовый класс и подкласс изменяемый представляет метод задания.

Так как C# не поддерживает этой модели, базовый класс должен иметь метод задания и метода получения, и могут использовать подкласс [OverrideAttribute](#OverrideAttribute).

Этот атрибут используется только в методах задания свойств и используется для поддержки изменяемый идиому в Objective-C.

Пример

```csharp
[BaseType (typeof (NSObject))]
interface MyString {
    [Export ("initWithValue:")]
    IntPtr Constructor (string value);

    [Export ("value")]
    string Value {
        get;

    [NotImplemented ("Not available on MyString, use MyMutableString to set")]
        set;
    }
}

[BaseType (typeof (MyString))]
interface MyMutableString {
    [Export ("value")]
    [Override]
    string Value { get; set; }
}
```

<a name="enum-attributes" />

## <a name="enum-attributes"></a>Атрибуты перечислений

Сопоставление `NSString` константы для значений перечисления — это простой способ создания улучшенный интерфейс API .NET. Он:

* позволяет завершение кода будут более полезны, отображая **только** правильные значения для API;
* Добавляет безопасность типа, нельзя использовать другое `NSString` константы в неправильный контекст; и
* позволяет скрыть нескольких констант, делая завершению кода отобразить короткий список API без потери функциональности.

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
```

В приведенном выше определении привязки создает генератор `enum` самого и создаст `*Extensions` статического типа, который включает методы два способа преобразования между значениями перечислений и `NSString` константы. Это означает, что константы остается доступной для разработчиков, даже если они не являются частью API-интерфейса.

Примеры

```csharp
// using the NSString constant in a different API / framework / 3rd party code
CallApiRequiringAnNSString (NSRunLoopMode.Default.GetConstant ());
```

```csharp
// converting the constants from a different API / framework / 3rd party code
var constant = CallApiReturningAnNSString ();
// back into an enum value
CallApiWithEnum (NSRunLoopModeExtensions.GetValue (constant));
```

### <a name="defaultenumvalueattribute"></a>DefaultEnumValueAttribute

Вы можете дополнить **один** значение перечисления с этим атрибутом. Это станет константа, возвращается, если значение перечисления неизвестен.

Из приведенного выше примера:

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Если значение перечисления не помечено то `NotSupportedException` будет создано.

### <a name="errordomainattribute"></a>ErrorDomainAttribute

Коды ошибок связываются как значения перечисления. Обычно имеется домен ошибок для их и не всегда легко найти, какой из них применяется (или если он еще существует).

Можно использовать этот атрибут должен быть сопоставлен перечисления ошибка домена.

Пример

```csharp
    [Native]
    [ErrorDomain ("AVKitErrorDomain")]
    public enum AVKitError : nint {
        None = 0,
        Unknown = -1000,
        PictureInPictureStartFailed = -1001
    }
```

Затем можно вызвать метод расширения `GetDomain` для получения константы ошибки домена.

### <a name="fieldattribute"></a>FieldAttribute

Это то же самое `[Field]` атрибут, используемый для констант внутри типа. Он также может использоваться внутри перечислений для сопоставления значения с определенной константы.

Объект `null` значение может использоваться для указания какое значение перечисления должен быть возвращен `null` `NSString` указана константа.

Из приведенного выше примера:

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Если не `null` значение присутствует элемент `ArgumentNullException` будет создано.

## <a name="global-attributes"></a>Глобальные атрибуты

Глобальные атрибуты применяются либо с помощью `[assembly:]` модификатор атрибута как [ `[LinkWithAttribute]` ](#LinkWithAttribute) или можно использовать везде, где, например [ `[Lion]` ](#SinceAndLionAttributes) и [ `[Since]` ](#SinceAndLionAttributes) атрибуты.

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

Это атрибут уровня сборки, который позволяет разработчикам указывать связывания флаги, необходимые для повторного использования связанных библиотеки без принудительного потребитель библиотеки, чтобы вручную настроить gcc_flags и mtouch дополнительные аргументы, переданные в библиотеку.

Синтаксис:

```csharp
// In properties
[Flags]
public enum LinkTarget {
    Simulator    = 1,
    ArmV6    = 2,
    ArmV7    = 4,
    Thumb    = 8,
}

[AttributeUsage(AttributeTargets.Assembly, AllowMultiple=true)]
public class LinkWithAttribute : Attribute {
    public LinkWithAttribute ();
    public LinkWithAttribute (string libraryName);
    public LinkWithAttribute (string libraryName, LinkTarget target);
    public LinkWithAttribute (string libraryName, LinkTarget target, string linkerFlags);
    public bool ForceLoad { get; set; }
    public string Frameworks { get; set; }
    public bool IsCxx { get; set;  }
    public string LibraryName { get; }
    public string LinkerFlags { get; set; }
    public LinkTarget LinkTarget { get; set; }
    public bool NeedsGccExceptionHandling { get; set; }
    public bool SmartLink { get; set; }
    public string WeakFrameworks { get; set; }
}
```

Этот атрибут применяется на уровне сборки, например, это [CorePlot привязки](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) использовать:

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

При использовании `[LinkWith]` атрибут, указанный `libraryName` внедряется в сборку, позволяя пользователям для отправки одной библиотеки DLL, содержащий неуправляемых зависимостей как флаги командной строки, необходимо правильно использовать библиотеку Xamarin.iOS.

Можно также запретить `libraryName`в этом случае `LinkWith` атрибута можно указывать только дополнительные флаги компоновщика:

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>Конструкторы LinkWithAttribute

Эти конструкторы позволяют пользователю указать библиотека для связывания с и внедрить в результирующей сборке, поддерживаемые целевые объекты, которые библиотека поддерживает и все флаги необязательно библиотеки, необходимые для библиотеки.

Обратите внимание, что `LinkTarget` аргумент неявно определяется Xamarin.iOS и не требуется задать.

Примеры

```csharp
// Specify additional linker:
[assembly: LinkWith (LinkerFlags = "-sqlite3")]

// Specify library name for the constructor:
[assembly: LinkWith ("libDemo.a");

// Specify library name, and link target for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator);

// Specify only the library name, link target and linker flags for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator, SmartLink = true, ForceLoad = true, IsCxx = true);
```

#### <a name="linkwithattributeforceload"></a>LinkWithAttribute.ForceLoad

`ForceLoad` Свойство используется, следует ли `-force_load` ссылку флаг используется для связывания собственной библиотеки. Сейчас это всегда должно быть true.

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

Если библиотеке привязки имеет является обязательным требованием для любой платформы (отличное от `Foundation` и `UIKit`), необходимо задать `Frameworks` свойство на строку, содержащую список необходимых платформ. Например, если вы привязываете библиотеку, требует `CoreGraphics` и `CoreText`, необходимо установить `Frameworks` свойства `"CoreGraphics CoreText"`.

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

Если установлено значение true, если полученный исполняемый файл должен быть скомпилирован с помощью компилятора C++ вместо значения по умолчанию, который является компилятором C. Используйте его, если библиотеки, в которой выполняется привязка был написан на C++.

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

Имя неуправляемые библиотеки для формирования пакета. Это файл с расширением «.a» и может содержать объектный код для нескольких платформ (для примера, ARM и x86 для симулятора).

Проверка более ранними версиями Xamarin.iOS `LinkTarget` свойства, чтобы определить поддерживаемые библиотеки платформы, но это определяется теперь автоматически и `LinkTarget` свойство учитывается.

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

`LinkerFlags` Строка предоставляет авторам привязки указать любые дополнительные флаги компоновщика требуется при связывании собственной библиотеки в приложение.

Например, если собственная библиотека требует libxml2 и zlib, пришлось бы установить `LinkerFlags` строка `"-lxml2 -lz"`.

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

Проверка более ранними версиями Xamarin.iOS `LinkTarget` свойства, чтобы определить поддерживаемые библиотеки платформы, но это определяется теперь автоматически и `LinkTarget` свойство учитывается.

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

Присвойте этому свойству значение true, если библиотеки, в которой выполняется связывание требуется библиотека обработка исключений GCC (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

`SmartLink` Свойство должно быть присвоено значение true, чтобы задать для Xamarin.iOS определить ли `ForceLoad` является обязательным или нет.

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

`WeakFrameworks` Так же, как работает свойство `Frameworks` свойства, за исключением случаев, во время компоновки `-weak_framework` описатель передается gcc для каждого из перечисленных платформ.

`WeakFrameworks` делает возможным для библиотек и приложений должна действовать слабая привязка от платформ, чтобы их при необходимости их можно использовать, если они доступны, но не принимают жесткие зависимости от их, что полезно, если библиотека предназначена для добавления дополнительных возможностей на более новые версии iOS. Дополнительные сведения о слабых ссылках см. в разделе документации Apple на [слабого связывания](http://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

Было бы хорошо подходят для слабого связывания `Frameworks` , такие как учетные записи, `CoreBluetooth`, `CoreImage`, `GLKit`, `NewsstandKit` и `Twitter` так, как они доступны только в iOS 5.

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) и LionAttribute (macOS)

Использовании `[Since]` атрибут флаг API-интерфейсы как имеющий проникновения в определенной точке во времени. Атрибут должен использоваться только для пометки типы и методы, которые могут вызвать проблемы среды выполнения, если базовый класс, метод или свойство не доступен.

Синтаксис:

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

Оно должно вообще не применяться для перечисления, ограничения или новые структуры так как те не приведет к ошибке времени выполнения, если они выполняются на устройстве с более старой версии операционной системы.

Пример, при применении к типу.

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

Пример, при применении к новому члену.

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

`[Lion]` Таким же образом, но для типов, представленных в Lion применяется атрибут. Причина использования `[Lion]` и более конкретные номер версии, который используется в iOS — что iOS пересматривается очень часто, хотя редко происходит основных выпусков OS X и проще запомнить, чем их кодовое имя, по их номеру версии операционной системы

### <a name="adviceattribute"></a>AdviceAttribute

Этот атрибут используется, чтобы предоставить разработчикам подсказку о других API-интерфейсы, которые могут быть более удобным для них.   Например если указать версию API со строгой типизацией, можно выполнить этот атрибут в атрибуте слабо типизированной для направления разработчику улучшенный интерфейс API.

Сведения из этого атрибута отображается в документации и средства могут разрабатываться для предоставления пользователю предложения по улучшению

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

Только в Xamarin.iOS 5.4 и более поздних.

Этот атрибут указывает, что генератор, привязка для этой определенной библиотеки (если применяется с `[assembly:]`) или типа следует использовать маршалинг строки быстрого копирования. Этот атрибут эквивалентен передача параметра командной строки `--zero-copy` генератора.

При использовании копирования для строк, генератор эффективно использует тот же C# строку как строку, которая использует Objective-C, не требуя создания нового `NSString` объекта и избежать копирования данных из C# строки к Objective-C-строка. Единственным недостатком использования копирования нуль строк является, что необходимо убедиться, что любое строковое свойство, можно переносить, происходит будет помечен как `retain` или `copy` имеет `[DisableZeroCopy]` набором атрибутов. Это нужно, так как дескриптор для копирования строки размещаются в стеке и является недопустимым после возврата функции.

Пример

```csharp
[ZeroCopyStrings]
[BaseType (typeof (NSObject))]
interface MyBinding {
    [Export ("name")]
    string Name { get; set; }

    [Export ("domain"), NullAllowed]
    string Domain { get; set; }

    [DisablZeroCopy]
    [Export ("someRetainedNSString")]
    string RetainedProperty { get; set; }
}

```

Можно также применить атрибут на уровне сборки, и он будет применяться ко всем типам сборки:

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>Словари со строгой типизацией

В Xamarin.iOS 8.0, мы добавили поддержку легко создавать строго типизированные классы, которые упаковывают `NSDictionaries`.

Хотя всегда можно было использовать [DictionaryContainer](xref:Foundation.DictionaryContainer) тип данных вручную интерфейс API, теперь стало гораздо проще сделать это.  Дополнительные сведения см. в разделе [отображая строгих типов](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

Когда этот атрибут применяется к интерфейсу, генератор создает класс с тем же именем, что интерфейс, который является производным от [DictionaryContainer](xref:Foundation.DictionaryContainer) и включает каждого свойства, определенного в интерфейсе в строго типизированный методы получения и задания для словаря.

Это автоматически создает класс, который может быть создан из существующего `NSDictionary` или который был создан новый.

Этот атрибут принимает один параметр, имя класса, содержащий ключи, которые используются для доступа к элементам в словаре.   По умолчанию каждое свойство в интерфейсе с атрибутом выполнит поиск члена в указанном типе для имени с суффиксом «Ключ».

Пример:

```csharp
[StrongDictionary ("MyOptionKeys")]
interface MyOption {
    string Name { get; set; }
    nint    Age  { get; set; }
}

[Static]
interface MyOptionKeys {
    // In Objective-C this is "NSString *MYOptionNameKey;"
    [Field ("MYOptionNameKey")]
    NSString NameKey { get; }

    // In Objective-C this is "NSString *MYOptionAgeKey;"
    [Field ("MYOptionAgeKey")]
    NSString AgeKey { get; }
}

```

В приведенном выше примере `MyOption` класс создаст строковое свойство для `Name` , будет использоваться `MyOptionKeys.NameKey` как ключ в словаре, извлекаемой строки.   И будет использовать `MyOptionKeys.AgeKey` как ключ в словаре, извлекаемой `NSNumber` , содержащее целого числа.

Если вы хотите использовать другой ключ, можно использовать атрибут export на свойство, например:

```csharp
[StrongDictionary ("MyColoringKeys")]
interface MyColoringOptions {
    [Export ("TheName")]  // Override the default which would be NameKey
    string Name { get; set; }

    [Export ("TheAge")] // Override the default which would be AgeKey
    nint    Age  { get; set; }
}

[Static]
interface MyColoringKeys {
    // In Objective-C this is "NSString *MYColoringNameKey"
    [Field ("MYColoringNameKey")]
    NSString TheName { get; }

    // In Objective-C this is "NSString *MYColoringAgeKey"
    [Field ("MYColoringAgeKey")]
    NSString TheAge { get; }
}
```

#### <a name="strong-dictionary-types"></a>Надежный словарь типов

Следующие типы данных поддерживаются в `StrongDictionary` определение:

|C#Тип интерфейса|`NSDictionary` Тип хранилища|
|---|---|
|`bool`|`Boolean` хранящиеся в `NSNumber`|
|Значения перечисления|целое число хранимых в `NSNumber`|
|`int`|32-разрядное целое число, хранящихся в `NSNumber`|
|`uint`|32-разрядное целое число без знака в `NSNumber`|
|`nint`|`NSInteger` хранящиеся в `NSNumber`|
|`nuint`|`NSUInteger` хранящиеся в `NSNumber`|
|`long`|64-разрядное целое число, хранящихся в `NSNumber`|
|`float`|32-разрядное целое число, хранятся в виде `NSNumber`|
|`double`|64-разрядное целое число, хранятся в виде `NSNumber`|
|`NSObject` и подклассы|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`Array` из `NSObject`|`NSArray`|
|C#`Array` перечислений|`NSArray` содержащий `NSNumber` значения|
