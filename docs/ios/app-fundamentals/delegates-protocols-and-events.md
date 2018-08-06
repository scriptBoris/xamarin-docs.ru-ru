---
title: События, протоколы и делегатов в Xamarin.iOS
description: В этом документе описано, как работать с событиями, протоколов и делегирует в Xamarin.iOS. Эти основные принципы присутствуют в Xamarin.iOS разработки.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d0e4c23bffe689c9218da2f43b97d98f348513ad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784014"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>События, протоколы и делегатов в Xamarin.iOS

Xamarin.iOS использует элементы управления для предоставления событий большинство взаимодействие с пользователем.
Приложения Xamarin.iOS принципы использования этих событий так же, как традиционных приложений .NET. Например класс Xamarin.iOS UIButton имеет событие вызывается TouchUpInside и использует это событие, как если бы этот класс и события были в приложении .NET.

Помимо этого подхода .NET Xamarin.iOS обеспечивает доступ к другой модели, который может использоваться для более сложного взаимодействия и привязки данных. Этот метод использует то, что вызывает Apple делегаты и протоколов. Делегаты — это напоминает делегаты в C#, но вместо определение и вызов один метод, делегат в Objective-C — целый класс, соответствующий протокол. Протокол — аналогично интерфейс в C#, за исключением того, его методы могут быть обязательными. Например чтобы заполнить UITableView с данными, создается классом делегата, реализует методы, определенные в протоколе UITableViewDataSource, UITableView нужно вызвать, чтобы заполнить сам.

В этой статье вы узнаете о всех этих разделов, обеспечивая надежную основу для обработки обратного вызова в Xamarin.iOS, включая:

-  **События** — событий с помощью .NET с элементами управления UIKit.
-  **Протоколы** — обучения, какие протоколы являются и как они используются, и Создание примера, который предоставляет данные для аннотации карты.
-  **Делегаты** — учебные о делегатах Objective-C расширение пример карты для управления взаимодействием с пользователем, который включает заметки, а затем обучения разницу между сильного и слабого делегаты и когда следует использовать каждый из них.

Чтобы проиллюстрировать протоколы и делегаты, мы выполним сборку простой схемы приложения, которое добавляет заметки к схеме, как показано ниже:

 [![](delegates-protocols-and-events-images/01-map.png "Пример приложения простой схемы, который добавляет заметки к схеме")](delegates-protocols-and-events-images/01-map.png#lightbox) [![](delegates-protocols-and-events-images/04-annotation-with-callout.png "пример заметки, добавление на карту")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Перед выполняемой этого приложения, давайте начнем, просмотрев события .NET с UIKit.

<a name=".NET_Events_with_UIKit" />

## <a name="net-events-with-uikit"></a>События .NET с UIKit

Xamarin.iOS предоставляет события .NET UIKit элементов управления. Например UIButton имеет событие TouchUpInside, который обрабатывать как обычно в .NET, как показано в следующем коде, лямбда-выражения C#:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```
Это может также реализовать с помощью C# 2.0 стиль анонимного метода похожее на следующее:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

Приведенный выше код, построенная в методе ViewDidLoad UIViewContoller. Переменная aButton ссылается кнопки, можно добавить в конструктор iOS или с кодом. На следующем рисунке показана эта кнопка, добавляемой в iOS конструктора, взяты из образца к этой статье:

 [![](delegates-protocols-and-events-images/02-interface-builder-outlet.png "Кнопка, добавленная в конструкторе iOS")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin.iOS также поддерживает стиль целевое действие подключения кода для взаимодействия, которая происходит при использовании элемента управления. Чтобы создать целевое действие кнопки "Hello", дважды щелкните его в конструкторе iOS. Откроется файл кода UIViewController и разработчик будет предложено выбрать расположение для вставки метод подключения:

 [![](delegates-protocols-and-events-images/03-interface-builder-action.png "Файл кода программной UIViewControllers")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

После выбора расположения новый метод создается и проводной доступ к элементу управления. В следующем примере сообщение записывается в консоль при нажатии кнопки:

 [![](delegates-protocols-and-events-images/05-interface-builder-action.png "Будет записано сообщение на консоль, при нажатии кнопки")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Дополнительные сведения о шаблоне целевое действие iOS см. разделе целевое действие « [компетенции основных компонентов приложения для iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)» в библиотеке разработчика iOS Apple.

Дополнительные сведения о том, как использовать конструктор iOS с помощью Xamarin.iOS см. в разделе « [iOS Общие сведения о конструкторе](~/ios/user-interface/designer/index.md)» документации.

 <a name="Events" />


## <a name="events"></a>События

Если требуется перехватывать события из UIControl, имеется ряд возможностей: с помощью C# лямбда-выражения и функции с помощью API-интерфейсы низкоуровневые Objective-C-делегаты.

В следующем разделе показано, как можно записать события TouchDown на кнопке, в зависимости от степени контроля необходимо.

 <a name="C#_Style" />


## <a name="c-style"></a>Стиль C#

С помощью синтаксиса делегат:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {    
    Console.WriteLine ("Touched");
};
```

Если вам нравится вместо лямбда-выражения:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Если вы хотите иметь несколько кнопок используйте тот же обработчик совместно использовать тот же код:

```csharp
void handler (object sender, EventArgs args)
{
   if (sender == button1)
      Console.WriteLine ("button1");
   else
      Console.WriteLine ("some other button");
}

button1.TouchDown += handler;
button2.TouchDown += handler;
```

<a name="Monitoring_more_than_one_kind_of_Event" />


## <a name="monitoring-more-than-one-kind-of-event"></a>Наблюдение за более чем один тип события

События для флагов UIControlEvent имеют взаимно-однозначное сопоставление для отдельных флагов. Если вы хотите иметь тот же участок кода обработки двух или более событий, используйте `UIControl.AddTarget` метод:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Используя синтаксис лямбда-выражения:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Если необходимо использовать низкоуровневые функции Objective-C, как подключение к экземпляру определенного объекта и вызова определенной селектора.

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

Обратите внимание, если вы реализуете метод экземпляр унаследованного базового класса, он должен быть открытый метод.

 <a name="Protocols" />


## <a name="protocols"></a>Протоколы

Протокол — это функция языка Objective-C, список элементов объявления метода. Он служит же цель, интерфейс в C#, основное отличие состоит в том, протокол может иметь необязательные методы. Необязательные методы не вызываются, если класс, который использует протокол не реализовывать их. Кроме того один класс в Objective-C можно реализовать несколько протоколов, так же, как класс C# может реализовывать несколько интерфейсов.

Apple использует протоколы на протяжении iOS определять контракты для классов, перейти к использованию, пока Воспринимая реализации класса из вызывающего, таким образом работы так же, как интерфейс C#. Протоколы будут использоваться в сценариях, не являющийся делегатом (такие как с `MKAnnotation` приведенном далее примере) и с помощью делегатов (как описанные далее в этом документе, в разделе делегаты).

 <a name="Protocols_with_Monotouch" />


### <a name="protocols-with-xamarinios"></a>Протоколы с Xamarin.ios

Давайте рассмотрим пример, используя протокол Objective-C из Xamarin.iOS. В этом примере мы будем использовать `MKAnnotation` протокола, который входит в состав из `MapKit` framework. `MKAnnotation` — Это протокол, позволяющий любой объект, который использует его для предоставления сведений о заметки, который может быть добавлен на карту. Например, объект, реализующий `MKAnnotation` предоставляет расположение заметки и заголовок, связанный с ним.

Таким образом `MKAnnotation` протокол используется для предоставления данных, сопровождающее заметки. Фактическое представление для заметки сам строится на основе данных в объект, который принимает `MKAnnotation` протокола. Например, текст выноски, которое появляется при нажатии кнопки на заметки (как показано на снимке экрана ниже) поставляется из `Title` свойства в классе, который реализует протокол:

 [![](delegates-protocols-and-events-images/04-annotation-with-callout.png "Пример текста для выноски, когда пользователь нажимает на заметку")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Как описано в следующем разделе протоколы глубокое погружение, Xamarin.iOS привязывает протоколы абстрактные классы. Для `MKAnnotation` протокола, связанного класса C# называется `MKAnnotation` для имитации имя протокола, который является подклассом `NSObject`, корневой базовый класс для CocoaTouch. Протокол требует Get и set должны быть реализованы для координаты; Тем не менее заголовок и подзаголовок являются необязательными. Таким образом, в `MKAnnotation` класса, `Coordinate` свойство *абстрактный*, запрашивать ее для реализации и `Title` и `Subtitle` свойства, отмеченные *виртуальный* , что делает их необязательно, как показано ниже:

```csharp
[Register ("MKAnnotation"), Model ]
public abstract class MKAnnotation : NSObject
{
    public abstract CLLocationCoordinate2D Coordinate
    {
        [Export ("coordinate")]
        get;
        [Export ("setCoordinate:")]
        set;
    }

    public virtual string Title
    {
        [Export ("title")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }

    public virtual string Subtitle
    {
        [Export ("subtitle")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }
...
}
```

Любой класс может предоставлять данные заметки, просто унаследовав `MKAnnotation`, при условии, что по крайней мере `Coordinate` свойство реализовано. Например ниже приведен пример класса, которая принимает координаты в конструкторе и возвращает строку для заголовка:

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string _title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        _title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return _title;
        }
    }
}
```

Он привязан к протоколу любого класса, который наследуется от класса `MKAnnotation` можно предоставить важные данные, которые будут использоваться схему при создании представления заметки. Добавление заметки к схеме, вызовите `AddAnnotation` метод `MKMapView` экземпляра, как показано в следующем коде:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
new CLLocationCoordinate2D (42.3467512, -71.0969456);

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

Переменной карты является экземпляром класса `MKMapView`, — класс, который представляет саму карту. `MKMapView` Будет использовать `Coordinate` данные, производные от `SampleMapAnnotation` экземпляр для размещения заметки представления на карте.

`MKAnnotation` Протокола предоставляет набор известных возможностей через объекты, реализующие его без потребителя (карты в данном случае) нужно знать о детали реализации. Это упрощает добавление различных возможных заметок к схеме.

 <a name="Protocols_Deep_Dive" />


### <a name="protocols-deep-dive"></a>Глубокое погружение протоколы

Так как интерфейсы C# не поддерживает необязательные методы, Xamarin.iOS сопоставляет протоколы абстрактные классы. Таким образом используют протокол в Objective-C выполняется в Xamarin.iOS, производные от абстрактного класса, к которому привязан протокол и реализующий необходимые методы. Эти методы будут предоставляться как абстрактные методы в классе. Необязательные методы от протокола будет привязан к виртуальные методы класса C#.

Например, вот часть `UITableViewDataSource` протокола, связанных в Xamarin.iOS:

```csharp
public abstract class UITableViewDataSource : NSObject
{
    [Export ("tableView:cellForRowAtIndexPath:")]
    public abstract UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath);
    [Export ("numberOfSectionsInTableView:")]
    public virtual int NumberOfSections (UITableView tableView){...}
...
}
```

Обратите внимание, что класс является абстрактным. Xamarin.iOS делает класс абстрактный для поддержки методов необязательные и необходимые протоколы.
Однако в отличие от протоколов Objective-C (или интерфейсы C#), классы C# не поддерживает множественное наследование. Это влияет на конструктор кода C#, который использует протоколы и обычно приводит к вложенным классам. Более об этой проблеме рассматривается далее в этом документе, в разделе делегатов.

 `GetCell(…)` — Абстрактный метод, привязанный к Objective-C *селектор*, `tableView:cellForRowAtIndexPath:`, являющийся обязательным метод `UITableViewDataSource` протокола. Селектор — это термин Objective-C для имени метода. Чтобы включить метод, при необходимости, Xamarin.iOS объявляющему его как абстрактный. Другой метод `NumberOfSections(…)`, привязанный к `numberOfSectionsInTableview:`. Этот метод является необязательным в протоколе, поэтому Xamarin.iOS объявляющему его как виртуальные, сделав его необязательное переопределение в C#.

Xamarin.iOS берет на себя все привязки операций ввода-вывода для вас. Тем не менее, если в дальнейшем вручную привязать протокол из Objective-C, это можно сделать с помощью оформления класса с `ExportAttribute`. Это тот же метод, используемый Xamarin.iOS сам.

Дополнительные сведения о том, как привязать типы Objective-C в Xamarin.iOS см. в статье [типы привязки Objective-C](~/ios/platform/binding-objective-c/index.md).

Мы еще не через с протоколами, хотя. Они также используются в iOS как основу для Objective-C делегаты, которые раздел в следующем разделе.

 <a name="Delegates" />


## <a name="delegates"></a>Делегаты

операций ввода-вывода использует Objective-C делегатов для реализации шаблона делегирования, в которой один объект передает рабочих в другой. Объект, выполняющий работу является делегатом первого объекта. Объект сообщает своего делегата для выполнения работы, отправляя его сообщения после происходят определенные события. Отправка сообщения следующим образом в Objective-C функционально эквивалентен при вызове метода в C#. Делегат, реализует методы в ответ на эти вызовы и поэтому предоставляет функциональные возможности для приложения.

Делегаты позволяют расширять поведение классов без необходимости создания подклассов. Приложения в iOS часто используют делегаты, когда один класс выполняет обратный вызов в другой после выполнения важные действия. Например `MKMapView` класса вызова своего делегата, когда пользователь касается заметки на карте, предоставляя возможность ответить в приложении автор класса делегата. Можно передвигаться по пример использования делегата далее в этой статье, в примере с помощью этого типа делегата с помощью Xamarin.iOS.

На этом этапе может возникнуть вопрос как класс определяет, какие методы для вызова своего делегата. Это еще один способ, в которых используются протоколы. Как правило методы, доступные для делегата берутся из протоколов, которые они принять.

 <a name="How_Protocols_are_used_with_Delegates" />


### <a name="how-protocols-are-used-with-delegates"></a>Использование протоколов в делегатах

Мы узнали, как ранее протоколы используются для поддержки Добавление заметок к схеме.
Протоколы также используются для предоставления известных набор методов для классов, чтобы вызвать после определенные события, происходящие, таких как после нажатия пользователем заметки на карте или ячейку в таблице. Классы, реализующие эти методы называются делегатов, классов, которые вызывают их.

Классы, которые поддерживают делегирование сделать, предоставление доступа к свойству делегата, которому присваивается класса, реализующего делегат. Методы, которые можно реализовать для делегата будут зависеть от протокол, который принимает определенного делегата. Для `UITableView` реализовать метод, `UITableViewDelegate` протокол, для `UIAccelerometer` метода, следует реализовать `UIAccelerometerDelegate`, и так далее для других классов в iOS, для которой требуется предоставить делегат.

`MKMapView` Мы видели в примере выше класса также имеет свойство с именем делегата, который он будет вызывать после возникновении различных событий. Делегат для `MKMapView` относится к типу `MKMapViewDelegate`.
Вы будете использовать это вскоре в пример реагировать на заметку после ее выбора, но сначала давайте обсудить разницу между сильного и слабого делегатов.

 <a name="Strong_Delegates_vs._Weak_Delegates" />


### <a name="strong-delegates-vs-weak-delegates"></a>Делегаты строгих vs. Слабый делегатов

Делегаты, которые мы рассмотрели, пока являются строгого делегаты, это означает, что они являются строго типизированными. Xamarin.iOS привязок, поставляемых вместе с строго типизированный класс для всех протоколов делегата в iOS. Тем не менее операций ввода-вывода также использует концепцию слабый делегат. Вместо создания подклассов класса, привязанного к протоколу Objective-C для определенного делегата, операций ввода-вывода также позволяет выбрать для привязки протокола методы самостоятельно в любом классе, вам нравится, производный от NSObject, декорирования методов с ExportAttribute, а затем указав соответствующие селекторов.
При использовании этого подхода можно присвоить экземпляр класса WeakDelegate свойство вместо свойства делегата. Слабый делегат обеспечивает гибкость класса делегата вниз по иерархии наследования различных вступили в силу. Давайте рассмотрим пример Xamarin.iOS, который использует сильного и слабого делегатов.

 <a name="Example_using_a_Delegate_with_Xamarin.iOS" />


### <a name="example-using-a-delegate-with-xamarinios"></a>Пример, использующий делегат с Xamarin.iOS

Чтобы выполнить код в ответ на пользователя, коснувшись заметки в нашем примере, мы можем подкласс `MKMapViewDelegate` и присвойте экземпляр `MKMapView`в `Delegate` свойство. `MKMapViewDelegate` Протокол содержит только необязательные методы.
Таким образом, все методы являются виртуальными, привязанных к этому протоколу в Xamarin.iOS `MKMapViewDelegate` класса. Когда пользователь выбирает заметки, `MKMapView` экземпляр будет отправлять `mapView:didSelectAnnotationView:` сообщение его делегату. Чтобы решить эту проблему в Xamarin.iOS, необходимо переопределить `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` метод в подклассе MKMapViewDelegate следующим образом:

```csharp
public class SampleMapDelegate : MKMapViewDelegate
{
    public override void DidSelectAnnotationView (
        MKMapView mapView, MKAnnotationView annotationView)
    {
        var sampleAnnotation =
            annotationView.Annotation as SampleMapAnnotation;

        if (sampleAnnotation != null) {

            //demo accessing the coordinate of the selected annotation to
            //zoom in on it
            mapView.Region = MKCoordinateRegion.FromDistance(
                sampleAnnotation.Coordinate, 500, 500);

            //demo accessing the title of the selected annotation
            Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
        }
    }
}
```

Класс SampleMapDelegate, показанный выше реализован как вложенный класс в контроллере, который содержит `MKMapView` экземпляра. Objective-C вы увидите часто применять несколько протоколов непосредственно в класс контроллера. Тем не менее Поскольку протоколы связаны с классами в Xamarin.iOS, классы, реализующие строго типизированные делегаты обычно включаются как вложенные классы.

С реализацией класса делегата в месте, необходимо только создавать экземпляр делегата в контроллере и назначьте его `MKMapView` `Delegate` свойства, как показано ниже:

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    SampleMapDelegate _mapDelegate;
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //set the map's delegate
        _mapDelegate = new SampleMapDelegate ();
        map.Delegate = _mapDelegate;
        ...
    }
    class SampleMapDelegate : MKMapViewDelegate
    {
        ...
    }
}
```

Использование слабый делегат для выполнения той же задачи, ее следует привязать метод самостоятельно в любой класс, производный от `NSObject` и назначьте его `WeakDelegate` свойство `MKMapView`. Так как `UIViewController` в конечном счете класс является производным от `NSObject` (например, каждый класс Objective-C в CocoaTouch) мы просто реализовать метод, привязанный к `mapView:didSelectAnnotationView:` непосредственно в контроллере и назначить контроллера `MKMapView` `WeakDelegate`, исключая необходимость использования дополнительного вложенного класса. Приведенный ниже код демонстрирует этот подход:

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        //assign the controller directly to the weak delegate
        map.WeakDelegate = this;
    }
    //bind to the Objective-C selector mapView:didSelectAnnotationView:
    [Export("mapView:didSelectAnnotationView:")]
    public void DidSelectAnnotationView (MKMapView mapView,
        MKAnnotationView annotationView)
    {
        ...
    }
}
```

При выполнении этого кода, что приложение ведет себя так же, как при работе строго типизированные делегат версии. Преимущество из данного кода заключается в слабый делегат не нужно создавать дополнительный класс, который был создан при использовании строго типизированной делегата. Тем не менее это достигается за счет строгую типизацию. Если требовалось допущена ошибка в селекторе, который был передан в `ExportAttribute`, вы бы определить до времени выполнения.

 <a name="Events_and_Delegates" />


### <a name="events-and-delegates"></a>События и делегаты

Делегаты используются для обратных вызовов в iOS, аналогично тому, как .NET использует события. Чтобы iOS API-интерфейсы и используют делегаты Objective-C способ показаться более .NET, Xamarin.iOS предоставляет события .NET во многих местах, где используются делегаты в iOS.

Например, более ранних реализация где `MKMapViewDelegate` ответил на выбранной заметки также быть реализован в Xamarin.iOS с помощью события .NET. В этом случае события должны быть определены в `MKMapView` и вызывается `DidSelectAnnotationView`. Они должны были `EventArgs` подкласс типа `MKMapViewAnnotationEventsArgs`. `View` Свойство `MKMapViewAnnotationEventsArgs` предоставит ссылку на представление заметки, из которого можно продолжить одну реализацию был ранее, как это показано ниже:

```csharp
map.DidSelectAnnotationView += (s,e) => {
    var sampleAnnotation = e.View.Annotation as SampleMapAnnotation;
    if (sampleAnnotation != null) {
        //demo accessing the coordinate of the selected annotation to
        //zoom in on it
        mapView.Region = MKCoordinateRegion.FromDistance (
            sampleAnnotation.Coordinate, 500, 500);

        //demo accessing the title of the selected annotation
        Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
    }
};
```

 <a name="Summary" />


## <a name="summary"></a>Сводка

В этой статье рассматривается, как использовать события, протоколы и делегаты в Xamarin.iOS. Мы узнали, каким образом Xamarin.iOS событиями обычный .NET стиль для элементов управления.
Далее мы узнали о протоколах Objective-C, включая как они отличаются от интерфейсов C# и как Xamarin.iOS использует их. Наконец мы рассмотрели Objective-C делегаты с точки зрения Xamarin.iOS. Мы узнали, как Xamarin.iOS поддерживает как строго и слабо типизированным делегаты и привязка событий .NET делегировать методы.


## <a name="related-links"></a>Связанные ссылки

- [Протоколы, делегаты и события (пример)](https://developer.xamarin.com/samples/Protocols_Delegates_Events/)
- [Привет, iOS](~/ios/get-started/hello-ios/index.md)
- [Типы C цель привязки](~/ios/platform/binding-objective-c/index.md)
- [Язык программирования C цель](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Разработка пользовательских интерфейсов в Xcode 4](http://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Компетенции основных компонентов приложения для iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
