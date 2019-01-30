---
title: Проектирование API Xamarin.iOS
description: В этом документе описываются некоторые из руководящих принципов, позволяют спроектировать API-интерфейсы Xamarin.iOS и как они связаны с Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 75904ad91df7795c538e736eabb6c6000847b449
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233657"
---
# <a name="xamarinios-api-design"></a>Проектирование API Xamarin.iOS

Помимо основных библиотеках базовых классов, которые являются частью Mono [Xamarin.iOS](http://www.xamarin.com/iOS) поставляется с привязками для iOS различные API-интерфейсы, позволяющие разработчикам создавать собственные приложения iOS с Mono.

В основе Xamarin.iOS, имеется механизм взаимодействия, связывающим мира C# с помощью Objective-C world, а также привязки для iOS на основе C API, например CoreGraphics и [OpenGL ES](#OpenGLES).

Низкоуровневые среда выполнения для связи с кодом Objective-C доступна в [MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime). Поверх этого, привязки для [Foundation](#MonoTouch.Foundation), CoreFoundation, и [UIKit](#MonoTouch.UIKit) предоставляются.

## <a name="design-principles"></a>Принципы проектирования

Вот некоторые из наших принципов проектирования для привязок Xamarin.iOS (они также применяются к Xamarin.Mac, Mono привязки для Objective-C в macOS):

- Выполните [рекомендации по разработке Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- Разрешить разработчикам подкласс Objective-C классы:

  - Являются производными от существующего класса
  - Вызывает базовый конструктор для цепочки
  - Переопределение методов должно выполняться с помощью C# переопределение системы
  - Подклассы должны работать с стандартные конструкции C#

- Не предоставляют разработчикам селекторы Objective-C
- Предоставляет механизм для вызова произвольных библиотек Objective-C
- Выполнять основные задачи Objective-C, простая и возможные задачи Objective-C
- Предоставляют свойства Objective-C в качестве свойства C#
- Предоставлять строго типизированным интерфейсом API:

  - Повышения безопасности типов
  - Свести к минимуму ошибки времени выполнения
  - Получение IntelliSense в интегрированной среде разработки на типы возвращаемого значения
  - Позволяет документацию всплывающее окно интегрированной среды разработки

- Рекомендуем IDE в изучении API-интерфейсы:

  - Например, вместо предоставления слабо типизированного массива, следующим образом:
    
    ```objc
    NSArray *getViews
    ```
    Предоставлять строгий тип, следующим образом:
    
    ```csharp
    NSView [] Views { get; set; }
    ```
    
    Это дает возможность автоматического завершения при просмотре API Visual Studio для Mac, делает все `System.Array` операций, доступных в возвращаемое значение и возвращаемое значение для участия в LINQ.

- Собственные типы C#:

  - [`NSString` становится `string`](~/ios/internals/api-design/nsstring.md)
  - Включить `int` и `uint` параметры, которые должны были перечислений в C# перечисления и перечисления C# с `[Flags]` атрибуты
  - А не зависящий от типа `NSArray` объекты, предоставляют массивов в качестве строго типизированных массивов.
  - Для событий и уведомлений предоставьте пользователям возможность выбора из:

    - Строго типизированную версию по умолчанию
    - Более слабо типизированной версии для вариантов расширенного использования

- Поддерживать шаблон Objective-C делегат:

    - Система событий в C#
    - Предоставить делегаты в C# (лямбда-выражения, анонимные методы и `System.Delegate`) для Objective-C API, что блоки

### <a name="assemblies"></a>Сборки

Xamarin.iOS содержит ряд для сборки, которые составляют *Xamarin.iOS профиль*. [Сборки](~/cross-platform/internals/available-assemblies.md) страница содержит дополнительные сведения.

### <a name="major-namespaces"></a>Основные пространства имен 

<a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

[ObjCRuntime](https://developer.xamarin.com/api/namespace/ObjCRuntime/) пространства имен позволяет разработчикам устранить мирами между C# и Objective-C.
Это новую привязку, предназначенные специально для iOS, основано на опыте Cocoa # и Gtk #.

<a name="MonoTouch.Foundation" />

#### <a name="foundation"></a>Foundation

[Foundation](xref:Foundation) пространство имен предоставляет базовые типы данных предназначен для взаимодействия с платформой Foundation Objective-C, который является частью iOS и является основой для объектно-ориентированного программирования в Objective-C.

Xamarin.iOS отражает в C# иерархию классов, от Objective-C. Например, базовый класс Objective-C [NSObject](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html) можно использовать с помощью C# с помощью [Foundation.NSObject](xref:Foundation.NSObject).

Несмотря на то, что это пространство имен обеспечивает привязки для базовых типов Foundation Objective-C, в некоторых случаях мы сопоставили базовые типы и типы .NET. Пример:

- Вместо того чтобы разбираться с [NSString](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html) и [сравнение NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), среда выполнения предоставляет их как C# [строка](xref:System.String)s и строго типизированные [массива](xref:System.Array)s на протяжении всего API.

- Различные интерфейсы API модуля поддержки доступны здесь позволяют разработчикам для привязки сторонних Objective-C API, других операций ввода-вывода API или API, в настоящее время не связаны с Xamarin.iOS.

Дополнительные сведения о привязке API-интерфейсов, см. в разделе [генератор привязки Xamarin.iOS](~/cross-platform/macios/binding/binding-types-reference.md) раздел.


##### <a name="nsobject"></a>NSObject

[NSObject](xref:Foundation.NSObject) тип является основой для всех привязок Objective-C. Типы Xamarin.iOS отражают два класса типов из cocoatouch, облегченную API для iOS: типы C (обычно называют типами CoreFoundation) и типы Objective-C (они являются производными от класса NSObject).

Для каждого типа, отражающую неуправляемого типа, можно получить через собственный объект [обрабатывать](xref:Foundation.NSObject.Handle) свойство.

Хотя Mono обеспечит сбор мусора для всех объектов, `Foundation.NSObject` реализует [System.IDisposable](xref:System.IDisposable) интерфейс. Это означает, что можно явно освободить ресурсы из любой заданной NSObject без ожидания для сборщика мусора для операций в. Это важно при использовании высоких NSObjects, например, UIImages, который может содержать указатели для больших блоков данных.

Если ваш тип должен осуществлять детерминированную финализацию, переопределить [NSObject.Dispose(bool) метод](xref:Foundation.NSObject.Dispose(System.Boolean)) параметра Dispose является «bool disposing», и если задать значение true, он означает, что метод Dispose вызывается, поскольку пользователь явно вызываемые Dispose (). в объекте. Если значение равно false, это означает, что метод Dispose (bool disposing) вызывается из метода завершения в потоке метода завершения. []()


##### <a name="categories"></a>Категории

Начиная с Xamarin.iOS 8.10 имеется возможность создавать категории Objective-C из C#.

Это делается с помощью `Category` атрибут, указывающие тип для расширения в качестве аргумента для атрибута. Следующий пример для экземпляра будут расширены NSString.

    [Category (typeof (NSString))]

Каждый метод категории использует стандартный механизм экспорта методов в Objective-C с помощью `Export` атрибут:

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

Все методы управляемые расширения должны быть статическими, но можно создать методы экземпляра Objective-C, используя стандартный синтаксис для методов расширения в C#:

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

и первый аргумент к методу расширения будет экземпляр, для которого был вызван метод.

Полный пример.

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
}
```

В этом примере добавляет метод экземпляра собственного toUpper классу NSString, который можно вызвать из Objective-C.

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAudoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

Один из сценариев, в которых это бывает удобно Добавление метода для всего набора классов в базе кода, например, это сделает все `UIViewController` экземпляров отчетов, что они вращаются:

```csharp
[Category (typeof (UINavigationController))]
class Rotation_IOS6 {
      [Export ("shouldAutorotate:")]
      static bool ShouldAutoRotate (this UINavigationController self)
      {
          return true;
      }
}
```


##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute — это настраиваемый атрибут, сообщающий mtouch — средство развертывания Xamarin.iOS — для сохранения типа или члена типа, на этапе, при обработке приложения для уменьшения его размера.

Все члены, которые не имеют статических ссылок из приложения, подлежат удалению. Таким образом этот атрибут используется для пометки членов, которые нет статических ссылок, но, по-прежнему необходимы вашему приложению.

Например, если вы динамически создаете экземпляры типов, для них нужно сохранять в коде конструктор по умолчанию. Если используется XML-сериализация, нужно сохранять свойства типов.

Этот атрибут можно применить для любого члена типа или для типа в целом. Если вы хотите сохранить весь тип, можно использовать синтаксис [Сохранить (AllMembers = true)] на тип.

<a name="MonoTouch.UIKit" />

#### <a name="uikit"></a>UIKit

[UIKit](xref:UIKit) пространство имен содержит взаимно-однозначное сопоставление для всех компонентов пользовательского интерфейса, составляющих cocoatouch, облегченную в виде классов C#. API был изменен для выполнения соглашения, используемые в языке C#.

Делегаты в C# предоставляются для выполнения распространенных операций. См. в разделе [делегаты](#Delegates) Дополнительные сведения.

<a name="OpenGLES" />

#### <a name="opengles"></a>OpenGLES

В отношении OpenGLES, мы распространять [измененная версия](https://developer.xamarin.com/api/namespace/OpenTK/) из [OpenTK](http://www.opentk.com/) API, объектно ориентированного привязку OpenGL, которые были изменены для использования CoreGraphics типы и структуры данных, а также предоставление доступа только к функции, которая доступна на устройствах iOS.

OpenGLES 1.1 функциональные возможности доступны в тип ES11.GL, задокументированы [здесь](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES11.GL/) типа.

OpenGLES 2.0 функциональные возможности доступны в тип ES20.GL, задокументированы [здесь](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES20.GL/) типа.

OpenGLES 3.0 функциональные возможности доступны в тип ES30.GL, задокументированы [здесь](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES30.GL/) типа.


### <a name="binding-design"></a>Привязка проектирования

Xamarin.iOS не просто привязки к базовой платформе Objective-C. Он расширяет систему типов .NET и диспетчеризации система лучше blend C# и Objective-C.

Так же, как P/Invoke — это полезное средство для вызова собственных библиотек в Windows и Linux, или как IJW поддержки может использоваться для COM-взаимодействия в Windows, Xamarin.iOS расширяет, среда выполнения поддерживает C# объекты привязки к объектам Objective-C.

Обсуждения в следующие несколько разделов не является обязательным для пользователей, создающих приложения Xamarin.iOS, которые помогут разработчикам понять, как выполняются действия, а поможет их при создании более сложных приложений.



#### <a name="types"></a>Типы

Где это было уместно, типы C# доступны в вместо низкого уровня типов Foundation, чтобы вселенной C#.  Это означает, что [API использует тип «string» C# вместо NSString](~/ios/internals/api-design/nsstring.md) и использует строго типизированные массивы в C# вместо предоставления сравнение NSArray.

Как правило, в проекте Xamarin.iOS и Xamarin.Mac, базовый `NSArray` объект не предоставляется. Вместо этого среда выполнения автоматически преобразует `NSArray`s, чтобы строго типизированные массивы некоторых `NSObject` класса. Таким образом Xamarin.iOS предоставляет метод слабо типизированной как getviews представления для возврата сравнение NSArray:

```csharp
NSArray GetViews ();
```

Вместо этого привязка предоставляет строго типизированный возвращаемое значение следующим образом:

```csharp
UIView [] GetViews ();
```

Существует несколько методов, предоставляемых в `NSArray`, для тупиковые ситуации, где вы можете использовать `NSArray` напрямую, но их использование не рекомендуется в API определения привязки.

Кроме того, в **классический API** вместо предоставления `CGRect`, `CGPoint` и `CGSize` из CoreGraphics API, обладающих мы заменили `System.Drawing` реализации `RectangleF`, `PointF`и `SizeF` — они бы помогут разработчикам сохранить существующий код OpenGL, использующий OpenTK. При использовании новый 64-разрядный **единый API**, следует использовать CoreGraphics API.

<a name="Inheritance" />

#### <a name="inheritance"></a>Наследование

Структура Xamarin.iOS API позволяет разработчикам расширять собственные типы Objective-C таким же образом, то, что они бы расширить тип C#, с помощью ключевого слова «переопределение» в производном классе, а также цепочки к базовой реализации с помощью ключевого слова C# «базовый».

Такой подход позволяет разработчикам избежать работы с селекторы Objective-C, как часть процесса разработки, так как вся система Objective-C уже упакован в библиотеки Xamarin.iOS.


#### <a name="types-and-interface-builder"></a>Типы и конструкторе Interface Builder

При создании классов .NET, которые являются экземплярами типов, созданные конструктором Interface Builder, вам нужно создать конструктор, который принимает один `IntPtr` параметра.
Это необходимо для привязки экземпляра управляемого объекта с неуправляемого объекта.
Код состоит из одной строки, следующим образом:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

<a name="Delegates" />


#### <a name="delegates"></a>Делегаты

Objective-C и C# имеют различные значения для делегата word на этих языках.

В мире Objective-C и в документации, вы найдете online о платформе CocoaTouch делегат обычно является экземпляром класса, который будет отвечать на несколько методов. Это очень похоже на C#-интерфейсом, с той разницей, что методы не всегда являются обязательными.

Эти делегаты играют важную роль в UIKit cocoatouch, облегченную интерфейсы API и других. Они используются для выполнения различных задач:

-  Для предоставления уведомления в код (аналогично Доставка событий в C# или Gtk +).
-  Для реализации модели для элементов управления для визуализации данных.
-  Чтобы добиться нужного поведения элемента управления.


Шаблон программирования был разработан, чтобы свести к минимуму создание производных классов для изменения поведения элемента управления. Это решение будет дух то было выполнено с годами других наборов средств графического пользовательского интерфейса: В Gtk сигнал, Qt слотов, события Winforms, WPF/Silverlight события и т. д. Чтобы избежать необходимости сотни интерфейса (по одному для каждого действия) или вносить разработчикам реализовать слишком много методов, которые не обязательно, Objective-C поддерживает необязательный метод определения. Это отличается от C# интерфейсы, которые требуют все методы должны быть реализованы.

В классах Objective-C, вы увидите, что классы, использующие этот шаблон программирования предоставляют свойство, которое обычно называется `delegate`, которая необходима, чтобы реализовать обязательные элементы интерфейса и нуль или более необязательных частей.

В Xamarin.iOS предлагаются три механизма взаимно исключают друг друга, для привязки к эти делегаты:

1.  [С помощью события](#Via_Events).
2.  [Строго типизированные через `Delegate` свойство](#StrongDelegate)
3.  [Слабо типизированные через `WeakDelegate` свойство](#WeakDelegate)

Например, рассмотрим [UIWebView](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html) класса. Это передает [UIWebViewDelegate](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html) экземпляр, который будет назначен [делегировать](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate) свойство.

<a name="Via_Events" />

##### <a name="via-events"></a>С помощью событий

Для многих типов Xamarin.iOS автоматически создаст соответствующий делегат, который будет пересылать `UIWebViewDelegate` звонков на C# события. Для `UIWebView`:

-  [WebViewDidStartLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:) относящимся [UIWebView.LoadStarted](xref:UIKit.UIWebView.LoadStarted) событий.
-  [WebViewDidFinishLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:) относящимся [UIWebView.LoadFinished](xref:UIKit.UIWebView.LoadFinished) событий.
-  [WebView:didFailLoadWithError](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:) относящимся [UIWebView.LoadError](xref:UIKit.UIWebView.LoadError) событий.

Например Эта простая программа записывает время начала и окончания, при просмотре веб-узел загрузки:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```


##### <a name="via-properties"></a>С помощью свойств

События полезны в тех случаях, когда может быть более одного подписчика на событие. Кроме того, события ограничены случаях там, где отсутствует возвращаемое значение из кода.

Для случаев, где код должен вернуть значение мы выбрали вместо этого для свойств. Это означает, что только один метод может быть задано в определенный момент времени в объекте.

Например, можно использовать этот механизм для убирания клавиатуры на экране на обработчик `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

`UITextField`В `ShouldReturn` свойство таким образом принимает в качестве аргумента делегата, который возвращает логическое значение и определяет ли TextField нужно делать что-то с возвращаемое нажатие кнопки. В нашем случае мы возвращаем *true* вызывающему объекту, но мы также удалить клавиатуру на экране (это происходит, когда вызывает textfield `ResignFirstResponder`).

<a name="StrongDelegate"/>

##### <a name="strongly-typed-via-a-delegate-property"></a>Строго типизированные через свойство делегата

Если вы не хотите использовать события, то можете предоставить собственные [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) подкласс и назначьте его [UIWebView.Delegate](xref:UIKit.UIWebView.Delegate) свойство. После назначения UIWebView.Delegate механизм диспетчеризации событий UIWebView больше не будет работать, и методы UIWebViewDelegate будет вызываться при возникновении соответствующего события.

Например этот простой тип записывает время, необходимое для загрузки веб-представление:

```csharp
class Notifier : UIWebViewDelegate  {
    DateTime startTime, endTime;

    public override LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    public override LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}
```

Оно используется в коде следующим образом:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Будет создан UIWebViewer и проинструктирует его для отправки сообщений на экземпляр Уведомитель, класс, который мы создали для ответа на сообщения.

Этот шаблон также используется для управления поведением для некоторых элементов управления, например в случае UIWebView [UIWebView.ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) свойство позволяет `UIWebView` экземпляр для элемента управления ли `UIWebView` загрузит страницы или нет.

Шаблон также используется для предоставления данных по запросу для нескольких элементов управления. Например [UITableView](xref:UIKit.UITableView) элемент управления является полем мощные прорисовки таблиц — и внешний вид и содержимое обусловлены экземпляр [UITableViewDataSource](xref:UIKit.UITableViewDataSource)

<a name="WeakDelegate"/>

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Слабо типизированные через свойство WeakDelegate

В дополнение к строго типизированные свойства имеется также слабый делегат типизированный, разработчик может по-разному при желании привязать.
Строго типизированный Everywhere `Delegate` свойство также представлено в привязке в Xamarin.iOS, соответствующий `WeakDelegate` также предоставляется свойство.

При использовании `WeakDelegate`, вы несете ответственность за правильно Декорирование класса с помощью [Экспорт](xref:Foundation.ExportAttribute) атрибут, чтобы задать область выделения. Пример:

```csharp
class Notifier : NSObject  {
    DateTime startTime, endTime;

    [Export ("webViewDidStartLoad:")]
    public void LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    [Export ("webViewDidFinishLoad:")]
    public void LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}

[...]

var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.WeakDelegate = new Notifier ();
```

Обратите внимание, что когда `WeakDelegate` назначено свойству, `Delegate` свойство не будет использоваться. Кроме того Если вы реализуете метод унаследованного базового класса, которую нужно [экспорта], необходимо сделать его открытого метода.


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>Сопоставление шаблона делегата Objective-C в C&#35;

Когда появится примеры Objective-C, которые выглядят следующим образом:

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

Это указывает, что язык для создания и создайте экземпляр класса «SomethingDelegate» и назначьте свойству делегат переменной foo. Этот механизм поддерживается Xamarin.iOS и C# синтаксис является:

```csharp
foo.Delegate = new SomethingDelegate ();
```

В Xamarin.iOS предоставлены строго типизированные классы, которые сопоставляются с Objective-C классов делегатов. Чтобы использовать их, вам будет использование подклассов и переопределение методов, определенных в Xamarin.iOS реализации. Дополнительные сведения о том, как они работают см. раздел «моделей» ниже.


##### <a name="mapping-delegates-to-c35"></a>Сопоставление делегатов в C&#35;

UIKit обычно использует делегаты Objective-C в двух формах.

Первая форма предоставляет интерфейс для компонента модели. Например как механизм для предоставления данных по запросу для представления, включая средств хранения данных для представления списка.  В этих случаях всегда должна создать экземпляр соответствующий класс, который и назначить переменной.

В следующем примере мы предоставляем `UIPickerView` с реализацией модели, который использует строки:

```csharp
public class SampleTitleModel : UIPickerViewTitleModel {

    public override string TitleForRow (UIPickerView picker, nint row, nint component)
    {
        return String.Format ("At {0} {1}", row, component);
    }
}

[...]

pickerView.Model = new MyPickerModel ();
```

Второй случай — для предоставления уведомления для события. В таких случаях несмотря на то, что мы по-прежнему предоставлять API в форме, описанные выше, мы также предоставляем C# события, которые должны быть проще в использовании для быстрого операций и интегрированная с анонимных делегатов и лямбда-выражения в C#.

Например, можно подписаться на `UIAccelerometer` события:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Доступны два варианта, там, где они смысл, но программист, вам необходимо выбрать одно из них. Если вы создаете собственный экземпляр строго типизированные респондент/делегата и назначить его, события C# не будет работать. При использовании событий C# методы в классе респондент/делегат никогда не будет вызываться.

Предыдущий пример, в котором используется `UIWebView` могут быть написаны с использованием лямбда-выражений C# 3.0, следующим образом:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>Реагирование на события

Иногда в код Objective-C, обработчики событий для различных элементов управления и поставщиков данных для нескольких элементов управления, будет размещаться в том же классе. Это можно сделать, так как классы отвечать на сообщения, и до тех пор, пока классы отвечать на сообщения, можно связывать объекты друг с другом.

Как описано ранее Xamarin.iOS поддерживает как C# на основе событий модель программирования, и шаблон делегат Objective-C, где можно создать новый класс, реализующий делегат и переопределяет необходимыми методами.

Можно также для поддержки шаблона Objective-C где ответчики для нескольких разных операций — все это размещается на том же экземпляре класса. Чтобы сделать это, однако, необходимо использовать низкоуровневые функции привязки Xamarin.iOS.

Например, если требуется, чтобы реагировать на оба класса `UITextFieldDelegate.textFieldShouldClear`: сообщение и `UIWebViewDelegate.webViewDidStartLoad`: в тот же экземпляр класса, пришлось бы использовать в объявлении атрибута [экспорта]:

```csharp
public class MyCallbacks : NSObject {
    [Export ("textFieldShouldClear:"]
    public bool should_we_clear (UITextField tf)
    {
        return true;
    }

    [Export ("webViewDidStartLoad:")]
    public void OnWebViewStart (UIWebView view)
    {
        Console.WriteLine ("Loading started");
    }
}
```

C# Имена методов не важны; все, которые важно, строк, передаваемых в атрибут [Export].

Если вы используете этот стиль программирования, убедитесь, что параметры C# соответствуют фактические типы, которые будут переданы в среде выполнения.

<a name="Models" />

#### <a name="models"></a>Модели

В средства хранения UIKit, или ответчики, которые реализуются с помощью вспомогательных классов они обычно именуются в код Objective-C делегаты, и они реализуются как протоколы.

Протоколы Objective-C похожи на интерфейсы, но они поддерживают необязательные методы – то есть не все методы должны быть реализованы для протокола для работы.

Существует два способа реализации модели. Можно реализовывать его вручную или использовать существующие определения строго типизированные.


Ручной механизм является обязательным, при попытке реализовать класс, который не привязан в Xamarin.iOS. Это очень просто:

-  Флаг класса для регистрации в среде выполнения
-  Примените атрибут [экспорта] с именем фактического выделения для каждого метода, который вы хотите переопределить
-  Создать экземпляр класса и передайте его.

К примеру следующие реализации только одного из вспомогательных методов в определении UIApplicationDelegate протокола:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

Имя селектора Objective-C ("applicationDidFinishLaunching:») объявлен с атрибутом экспорта и класс регистрируется с `[Register]` атрибута.

Xamarin.iOS предоставляет строго типизированный объявления, готова к использованию, не требующие привязку вручную. Чтобы обеспечить поддержку этой модели программирования, среда выполнения Xamarin.iOS поддерживает атрибут [модель] в объявлении класса. Это информирует среду выполнения, он должен не подключить все методы в классе, если методы, реализованный явным образом.

Это означает, что в UIKit, классы, представляющие протокол с помощью вспомогательных методов написаны следующим образом:

```csharp
[Model]
public class SomeViewModel : NSObject {
    [Export ("someMethod:")]
    public virtual int SomeMethod (TheView view) {
       throw new ModelNotImplementedException ();
    }
    ...
}
```

Если вы хотите реализовать модель, которая реализует только некоторые методы, что необходимо сделать всего лишь для переопределения методов и игнорировать других методов. Среда выполнения будет только подключить перезаписан методы, не исходного во всем мире Objective-C.

Приведен эквивалент предыдущего примера вручную.

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

Преимущества заключаются в том, что нет необходимости для файлов заголовков Objective-C, чтобы найти в селекторе, типы аргументов или сопоставление с C# во всех подробностях и что появится intellisense из Visual Studio для Mac, а также строгих типов


#### <a name="xib-outlets-and-c35"></a>Переменные экземпляров XIB и C&#35;

> [!IMPORTANT]
> В этом разделе объясняется интеграция с IDE с помощью переменных экземпляров при использовании файлов XIB. При использовании конструктора Xamarin для iOS, это все заменяется, введя имя в разделе **удостоверений > имя** в разделе "Свойства" в интегрированной среде разработки, как показано ниже:
>
> [![](images/designeroutlet.png "Введите имя элемента в конструкторе iOS")](images/designeroutlet.png#lightbox)
>
>Дополнительные сведения о конструкторе iOS, см. в статье [введение в конструктор iOS](~/ios/user-interface/designer/introduction.md#how-it-works) документа.

Это представляет собой низкоуровневый Описание интеграции розеток с помощью C# и предоставляется для опытных пользователей для Xamarin.iOS. Когда с помощью Visual Studio для Mac сопоставление выполняется автоматически в фоновом с помощью автоматически созданного кода во время полета автоматически.

При разработке пользовательского интерфейса с помощью конструктора Interface Builder, вы только будет создаваться внешний вид приложения и будет устанавливать некоторые подключения по умолчанию. Если вы хотите программным способом получения сведений о, изменять поведение элемента управления во время выполнения или изменение элемента управления во время выполнения, это необходимо для привязки элементов управления для управляемого кода.

Это делается за несколько шагов:

1.  Добавить **объявление переменной экземпляра** для вашей **владелец файла**.
1.  Подключение элемента управления **владелец файла**.
1.  Store пользовательского интерфейса, а также подключения в файле XIB или NIB.
1.  Загрузите NIB-файл во время выполнения.
1.  Доступ к переменной экземпляра переменной.


Действия (1) до (3) описаны в документации компании Apple для создания интерфейсов с помощью конструктора Interface Builder.

При использовании Xamarin.iOS, приложение должно создать класс, производный от UIViewController. Он реализован его следующим образом:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Затем для загрузки вашего ViewController из NIB-файл, это можно сделать:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Это загружает пользовательский интерфейс из NIB. Теперь для доступа к розеток, бывает необходимо указать среде выполнения, которую нам нужно получить к ним доступ. Чтобы сделать это, `UIViewController` подкласс необходимо объявить свойства и в них заметки с атрибутом [подключение]. Пример:

```csharp
[Connect]
UITextField UserName {
    get {
        return (UITextField) GetNativeField ("UserName");
    }
    set {
        SetNativeField ("UserName", value);
    }
}
```

Реализация свойств является тот, который фактически извлекает и сохраняет значение фактического собственного типа.

Не нужно беспокоиться об этом при использовании Visual Studio для Mac и InterfaceBuilder. Visual Studio для Mac автоматически отражает все выходы, объявленный с кодом в разделяемый класс, который компилируется как часть проекта.

#### <a name="selectors"></a>Селекторы

Основное понятие программирования Objective-C является селекторов. Интерфейсы API, которые требуют передавать селектор, или ожидает, что код, чтобы реагировать на них селектора часто встречается.

Создание нового селекторы в C# очень легко, просто создайте новый экземпляр класса `ObjCRuntime.Selector` класса и использования результата в любом месте в API, которому он требуется. Пример:

```csharp
var selector_add = new Selector ("add:plus:");
```

Для C# метод ответ на вызов селектор, он должен наследовать `NSObject` типа и выбранного метода C# должен быть снабжен атрибутом селектор имен с помощью `[Export]` атрибута. Пример:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Обратите внимание, что селектор имен **необходимо** должны точно совпадать, включая все промежуточные и конечные двоеточия (»:»), если он указан.

#### <a name="nsobject-constructors"></a>Конструкторы NSObject

Большинство классов в Xamarin.iOS, который является производным от `NSObject` предоставляет конструкторы, относящиеся к функции объекта, но они также будут предоставлены различные конструкторы, которые не сразу очевидно.

Конструкторы используются следующим образом:

```csharp
public Foo (IntPtr handle)
```

Этот конструктор используется для создания экземпляра класса, когда требуется сопоставить ваш класс неуправляемый класс среды выполнения. Это происходит, когда вы загружаете файл XIB/NIB-файле.  На этом этапе выполнения Objective-C будет создан объект в неуправляемой среде, и этот конструктор будет вызываться для инициализации управляемой стороне.

Как правило вам нужно всего лишь вызвать базовый конструктор с параметром дескриптор и в тексте, инициализация, необходимое.

```csharp
public Foo ()
```

Этот конструктор используется по умолчанию для класса и в Xamarin.iOS, предоставляемые классы, это инициализирует класс Foundation.NSObject и все классы, между ними и в конце, добавляет это Objective-c `init` метода в классе.

```csharp
public Foo (NSObjectFlag x)
```

Этот конструктор используется для инициализации экземпляра, но предотвратить метода Objective-C «init» в конце кода. Это обычно используется, если вы уже зарегистрировались для инициализации (при использовании `[Export]` на ваш конструктор) или если вы уже сделали инициализации через другой среднее.

```csharp
public Foo (NSCoder coder)
```

Этот конструктор предназначен для случаев, где инициализации объекта из экземпляра NSCoding. Дополнительные сведения см. в разделе Apple [архивы и руководство по программированию сериализации.](http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>Исключения

Проектирование Xamarin.iOS API не вызывают исключения Objective-C, как C# исключения. Структура обеспечивает, что не мусора отправляться во всем мире Objective-C в первую очередь и что все исключения, которые должны создаваться создаются по самой привязке прежде, чем когда-нибудь будет недопустимые данные, передаваемые world Objective-C.

#### <a name="notifications"></a>Уведомления

В iOS и OS X разработчики могут подписаться на уведомления, передаваемого базовой платформой. Это делается с помощью `NSNotificationCenter.DefaultCenter.AddObserver` метод. `AddObserver` Метод принимает два параметра; он уведомление, которое вы хотите подписаться на другой — метод, вызываемый при возникновении уведомления.

В Xamarin.iOS и Xamarin.Mac ключи для различных уведомлений размещаются на класс, который активирует уведомления. Например, уведомления, вызванных `UIMenuController` размещены как `static NSString` свойств в `UIMenuController` классы, которые заканчиваются имя «Уведомления».

### <a name="memory-management"></a>Управление памятью

Xamarin.iOS имеет сборщику мусора, что позаботится о освобождая ресурсы для вас, когда они больше не используются. В дополнение к сборщику мусора все объекты, производные от `NSObject` реализовать `System.IDisposable` интерфейс.

#### <a name="nsobject-and-idisposable"></a>NSObject и IDisposable

Предоставление доступа к `IDisposable` интерфейс является удобным способом помощь разработчикам в освобождение объектов, которые могут инкапсулировать большие блоки памяти (например, `UIImage` может выглядеть так же указатель безобидно, но может указывать на образ 2 мегабайта ) и другие важные и конечное ресурсы (например, декодирования видео буфер).

NSObject реализует интерфейс IDisposable, а также [шаблон .NET Dispose](http://msdn.microsoft.com/library/fs2xkftw.aspx). Это позволяет разработчикам Этот подкласс NSObject переопределить поведение Dispose и освободить свои ресурсы по запросу. Например рассмотрим этого контроллера представлений, которая обеспечивает вокруг множество изображений:

```csharp
class MenuViewController : UIViewController {
    UIImage breakfast, lunch, dinner;
    [...]
    public override void Dispose (bool disposing)
    {
        if (disposing){
             if (breakfast != null) breakfast.Dispose (); breakfast = null;
             if (lunch != null) lunch.Dispose (); lunch = null;
             if (dinner != null) dinner.Dispose (); dinner = null;
        }
        base.Dispose (disposing)
    }
}
```

При удалении управляемого объекта, он не используется. По-прежнему возможно, ссылку на объекты, но недопустимый объект для исчезнувшим на этом этапе. Некоторые интерфейсы API .NET обеспечить это, выдав ObjectDisposedException при попытке получить доступ к все методы удаленного объекта, например:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Даже если по-прежнему можно получить доступ к переменной «образ», это действительно недопустимую ссылку и больше не указывает на объект Objective-C, в которой содержалось изображение.

Но ликвидации объекта на языке C# не означает, что объект обязательно будут уничтожены. Необходимо всего лишь освобождение ссылки, для C# к объекту. Вполне возможно, что среда Cocoa может сохранил ссылку вокруг для внутреннего использования. К примеру, если свойство UIImageView изображение изображения, а затем dispose изображения, базовой UIImageView сделали собственную ссылку и сохранят ссылку на этот объект до завершения его использования.

#### <a name="when-to-call-dispose"></a>Когда следует вызвать Dispose

Вы должны вызвать Dispose, при необходимости Mono в избавлении от объекта. Случай из возможных способов использования — Mono не имеет никакого, что ваш NSObject фактически содержит ссылку к важному ресурсу, такие как память или пул сведения. В таких случаях следует вызывать метод Dispose для немедленно освободить ссылку на область памяти, а не ждать Mono для выполнения цикла сборки мусора.

На внутреннем уровне при Mono создает [NSString ссылается на основе C# строк](~/ios/internals/api-design/nsstring.md), он удаляет их немедленно, чтобы уменьшить объем работы, сборщик мусора приходится делать. Чем меньше объектов решения для дело, тем быстрее сборщик Мусора будет выполняться.

#### <a name="when-to-keep-references-to-objects"></a>Когда следует хранить ссылки на объекты

Одним из побочных эффектов, имеющий автоматическое управление памятью является то, что сборщик Мусора будет удалить неиспользуемые объекты до тех пор, пока нет ссылок на них. Это иногда может иметь неожиданные побочные эффекты, например, если вы создаете локальную переменную для хранения контроллере представление верхнего уровня, или окна верхнего уровня, а затем используя эти упразднены за вас.

Если не сохранять ссылку в ваш статический или экземпляр переменные для объектов, Mono будет вызывать метод Dispose() на них, и они будет освобождать ссылку на объект. Так как это может быть только необработанные ссылки, среде выполнения Objective-c. уничтожит объект для вас.

## <a name="related-links"></a>Связанные ссылки

- [Привязка полей](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
