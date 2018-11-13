---
title: События, протоколы и делегаты в Xamarin.iOS
description: В этом документе описывается, как работать с событиями, протоколы и делегирует в Xamarin.iOS. Эти фундаментальные концепции вездесущи в разработки Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/17/2017
ms.openlocfilehash: 83f9651fa7fd20709c620258833ae4a152ffd0eb
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563671"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>События, протоколы и делегаты в Xamarin.iOS

Xamarin.iOS использует элементы управления для предоставления событий, для большинства взаимодействий с пользователем.
Приложения Xamarin.iOS использовать эти события в так же, как традиционных приложений .NET. Например класс Xamarin.iOS UIButton имеет события с именем TouchUpInside и использует это событие, просто так, будто этим классом и событий в приложении .NET.

Помимо этот подход .NET Xamarin.iOS предоставляет другой модели, который может использоваться для более сложного взаимодействия и привязки данных. Эта методика использует того, что Apple называет делегатах и протоколах. Делегаты аналогичны в концепцию делегатов в C#, но чем определение и вызов одного метода, делегат в Objective-C является весь класс, который соответствует протоколу. Протокол похож на интерфейс в C#, за исключением того, что его методов могут быть необязательным. Например для заполнения UITableView с данными, вы создадите класс делегата, который реализует методы, определенные в протоколе UITableViewDataSource, UITableView нужно вызвать, чтобы заполнить сам.

В этой статье вы узнаете о всех этих разделов, предоставляя надежный фундамент для обработки сценариев обратного вызова в Xamarin.iOS, включая:

- **События** — событий с помощью .NET с помощью элементов управления UIKit.
- **Протоколы** — обучения, что протоколы являются и как они используются, и создание пример, который предоставляет данные для заметки в виде карты.
- **Делегаты** — обучение о делегатах Objective-C, расширим пример карты для управления взаимодействием с пользователем, который включает в себя заметки, а затем обучения различие между делегатами сильного и слабого узлов и когда следует использовать каждый из них.

Чтобы проиллюстрировать, протоколы и делегаты, мы создадим простую карту приложение, которое добавляет заметки к схеме, как показано ниже:

[![](delegates-protocols-and-events-images/01-map-sml.png "Пример приложения простую карту, которая добавляет заметки к схеме")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "пример заметки добавлены на карту")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Прежде чем браться это приложение, давайте начнем с рассмотрения событий .NET в UIKit.

## <a name="net-events-with-uikit"></a>События .NET с UIKit

Xamarin.iOS предоставляет события .NET для элементов управления UIKit. Например, UIButton имеет событие TouchUpInside, так как обрабатывать как обычно в .NET, как показано в следующем коде, который использует C# лямбда-выражения:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

Можно также реализовать это с C# анонимного метода в стиле 2.0 следующего вида:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

Приведенный выше код запустил `ViewDidLoad` метод UIViewController. `aButton` Переменная ссылается на кнопке, можно добавить в конструкторе iOS или с помощью кода. На следующем рисунке показана кнопка, которая была добавлена в конструкторе iOS:

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "Кнопки, добавленной в конструкторе iOS")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin.iOS также поддерживает целевое действие стиль подключение кода к взаимодействия, которая происходит при использовании элемента управления. Чтобы создать целевой объект действие для **Hello** "Кнопка", дважды щелкните его в конструкторе iOS. Откроется файл кода UIViewController и разработчику будет предложено выбрать расположение для вставки метод подключения:

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "Файл с выделенным кодом UIViewControllers")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

После выбора расположения нового метода создается и привязав к элементу управления. В следующем примере сообщение записывается в консоль при нажатии кнопки:

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "Сообщение записывается в консоль при нажатии кнопки")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Дополнительные сведения о шаблоне целевое действие iOS, см. в разделе целевое действие из [основной компетенции приложения для iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html) в библиотеке разработчика iOS Apple.

Дополнительные сведения о том, как использовать конструктор iOS с помощью Xamarin.iOS см. в разделе [iOS Общие сведения о конструкторе](~/ios/user-interface/designer/index.md) документации.

## <a name="events"></a>События

Если требуется перехватывать события из UIControl, у вас есть несколько вариантов: с помощью C# лямбда-выражения и функции делегата с помощью низкоуровневых API Objective-C.

В следующем разделе показано, как будет записывать события TouchDown на кнопке, в зависимости от степени контроля, вам потребуется.

## <a name="c-style"></a>C#Стиль

С помощью синтаксиса делегат:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

Если вам нравится лямбда-выражения вместо:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Если вы хотите иметь несколько кнопки используют тот же обработчик для совместного использования того же кода:

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

## <a name="monitoring-more-than-one-kind-of-event"></a>Наблюдение за более чем одного вида событий

C# События для флагов UIControlEvent имеют взаимно-однозначное сопоставление отдельные флаги. Если вы хотите иметь тот же участок кода обработки двух или более событий, используйте `UIControl.AddTarget` метод:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Используя синтаксис лямбда-выражения:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Если вам потребуется использовать низкоуровневые функции Objective-C, как подключение к конкретному экземпляру объекта и вызова определенной селектора.

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

Обратите внимание, что если вы реализуете метод экземпляра в наследуемый базовый класс, он должен быть открытый метод.

## <a name="protocols"></a>Протоколы

Протокол — это компонент языка Objective-C, предоставляющий список объявления метода. Он выполняет роль, похожую на интерфейс в C#, основным же различием является, что протокол могут иметь необязательные методы. Необязательные методы не вызываются, если класс, который использует протокол не устанавливает их. Кроме того, один класс в Objective-C можно реализовать множество протоколов, так же, как C# класс может реализовывать несколько интерфейсов.

Apple использует протоколы на протяжении всего операций ввода-вывода для определения контрактов для классов, внедрению, абстрагируясь от реализации класса от вызывающей стороны, таким образом операционных так же, как C# интерфейс. Протоколы используются оба в сценариях, не являющийся делегатом (например с `MKAnnotation` примера, показанного далее) и с делегатами (как описанные далее в этом документе в разделе делегаты).

### <a name="protocols-with-xamarinios"></a>Протоколы с помощью Xamarin.ios

Давайте рассмотрим пример с помощью протокола Objective-C с Xamarin.iOS. В этом примере мы будем использовать `MKAnnotation` протокола, входящий в состав из `MapKit` framework. `MKAnnotation` — Это протокол, позволяющий любой объект, который использует его для предоставления сведений о заметка, которую можно добавить на карту. Например, объект, реализующий интерфейс `MKAnnotation` предоставляет расположение заметки и заголовок, связанный с ним.

В этом случае `MKAnnotation` протокол используется для предоставления данных, сопровождающий заметки. Фактическим представлением для аннотации, сам строится на основе данных в объект, который принимает `MKAnnotation` протокола. Например, текст выноски, отображаемый, когда пользователь касается заметку (как показано на снимке экрана ниже) поставляется из `Title` свойства в классе, который реализует протокол:

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "Пример текста для выноски, когда пользователь касается заметку")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Как описано в следующем разделе [протоколы глубокое погружение в обработку](#protocols-deep-dive), Xamarin.iOS привязывает протоколы для абстрактных классов. Для `MKAnnotation` протокол, значение границы C# класс называется `MKAnnotation` имитировать имя протокола, и она является подклассом `NSObject`, корневой базовый класс для cocoatouch, облегченную. Протокол требует метод считывания и метод задания должны быть реализованы для координаты по оси; Тем не менее заголовок и подзаголовок являются необязательными. Таким образом, в `MKAnnotation` класс, `Coordinate` свойство *абстрактный*, запрашивать ее для реализации и `Title` и `Subtitle` свойства отмечены *виртуального* , что делает их необязательно, как показано ниже:

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

Любой класс можно предоставить данных заметок, просто наследование от `MKAnnotation`, при условии, что по крайней мере `Coordinate` свойство реализовано. Например ниже приведен пример класса, которая принимает координату в конструкторе и возвращает строку для заголовка:

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return title;
        }
    }
}
```

Он привязан к протоколу любого класса, который наследуется от класса `MKAnnotation` может предоставить важные данные, которые будут использоваться карты при создании представления заметки. Чтобы добавить заметку к схеме, просто вызовите `AddAnnotation` метод `MKMapView` экземпляра, как показано в следующем коде:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

Здесь переменная карты является экземпляром класса `MKMapView`, — класс, который представляет саму карту. `MKMapView` Будет использовать `Coordinate` данные, производные от `SampleMapAnnotation` экземпляр для размещения представления заметки на карте.

`MKAnnotation` Протокола предоставляет полный набор известных возможностей через объекты, реализующие его, при этом потребителя (map в данном случае) не требуется знать детали реализации. Это упрощает добавление различных возможных заметок на карту.

### <a name="protocols-deep-dive"></a>Подробный обзор протоколов

Так как C# интерфейсы не поддерживают необязательные методы, Xamarin.iOS сопоставляется протоколы абстрактные классы. Таким образом внедрение протокола в Objective-C выполняется в Xamarin.iOS путем наследования от абстрактного класса, который привязан к протоколу и реализующий необходимые методы. Эти методы будут предоставляться как абстрактные методы в классе. Необязательные методы протокола будет привязан к виртуальные методы C# класса.

Например, вот часть `UITableViewDataSource` протокола, привязанного в Xamarin.iOS:

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

Обратите внимание на то, что этот класс является абстрактным. Xamarin.iOS делает класс абстрактный для поддержки методов необязательно/необходимые протоколы.
Тем не менее, в отличие от протоколов Objective-C (или C# интерфейсы), C# классов не поддерживает множественное наследование. Это влияет на макет C# код, который использует протоколы и обычно приводит к вложенным классам. Более этот вопрос рассматривается далее в этом документе в разделе делегаты.

 `GetCell(…)` — Абстрактный метод, привязанного к Objective-C *селектор*, `tableView:cellForRowAtIndexPath:`, являющийся обязательным метод `UITableViewDataSource` протокола. Селектор — это термин Objective-C для имени метода. Чтобы применить метод, при необходимости, Xamarin.iOS оно объявлено как абстрактный. Другой метод, `NumberOfSections(…)`, привязанного к `numberOfSectionsInTableview:`. Этот метод является необязательным в протоколе, поэтому Xamarin.iOS оно объявлено как виртуальный, делая необязательно для переопределения в C#.

Xamarin.iOS берет на себя все привязки операций ввода-вывода для вас. Тем не менее, если вы когда-либо придется вручную привязать протокол из Objective-C, это можно сделать с помощью оформления класс с `ExportAttribute`. Это тот же метод, используемый Xamarin.iOS сам.

Дополнительные сведения о привязке Objective-C типы в Xamarin.iOS см. в статье [типы привязки Objective-C](~/ios/platform/binding-objective-c/index.md).

Мы еще не через с протоколами, однако. Они также используются в iOS в качестве основы для Objective-C делегаты, которые является темой в следующем разделе.

## <a name="delegates"></a>Делегаты

iOS использует делегаты Objective-C, чтобы реализовать шаблон делегирования, в которой один объект передает рабочих в другой. Объект, выполняющий работу является делегатом первого объекта. Объект сообщает его делегат для выполнения работы, отправив его сообщения после выполняются определенные действия. Отправка примерно следующее сообщение в Objective-C является функциональным эквивалентом при вызове метода в C#. Делегат реализует методы в ответ на такие вызовы и поэтому предоставляет функциональные возможности для приложения.

Делегаты позволяют расширять поведение классов без необходимости создания подклассов. Приложения в iOS часто используют делегаты, когда один класс вызывает обратно в другой, после выполнения важные действия. Например `MKMapView` класса вызовы обратно к его делегат, когда пользователь касается заметки на карте, предоставляя возможность ответить в приложении автор класса делегата. Вы сможете выполнять пример этого типа делегата использования далее в этой статье, в примере с помощью делегата с помощью Xamarin.iOS.

На этом этапе может возникнуть вопрос, как класс определяет, какие методы для вызова в его делегат. Это другое место, где используются протоколы. Как правило методы, доступные для делегата поступать из протоколов, которые они будут принять.

### <a name="how-protocols-are-used-with-delegates"></a>Использование протоколов с помощью делегатов

Мы видели ранее как протоколы используются для поддержки добавления заметок к схеме.
Протоколы также используются для предоставления известных набор методов для классов должна вызываться после определенного события происходят, таких как после нажатия пользователем заметки на карте или ячейку в таблице. Классы, реализующие эти методы называются делегатов, классов, которые вызывают их.

Классы, поддерживающие делегирование сделать это, предоставляя свойства делегата, которому присваивается класс, реализующий делегат. Методы, которые можно реализовать для делегата будут зависеть от протокола, который принимает определенного делегата. Для `UITableView` реализации метода, `UITableViewDelegate` протокол, для `UIAccelerometer` метод, необходимо реализовать `UIAccelerometerDelegate`, и так далее для других классов в iOS, для которого следует предоставить делегат.

`MKMapView` Класс, мы видели в примере выше, также имеет свойство с именем делегат, который вызовет после возникновении различных событий. Делегат для `MKMapView` имеет тип `MKMapViewDelegate`.
Вы будете использовать это чуть позже в пример реагировать на заметку после ее выбора, но сначала давайте обсудим различие между делегатами сильного и слабого узлов.

### <a name="strong-delegates-vs-weak-delegates"></a>Делегаты строгих vs. Слабых делегатах

Делегаты, которые мы рассмотрели до сих — это надежный делегаты, это означает, что они являются строго типизированными. Привязок Xamarin.iOS поставляется с строго типизированный класс для всех протоколов делегата в iOS. Тем не менее операций ввода-вывода также использует концепцию слабый делегат. Вместо создания подклассов класс привязан к протоколу Objective-C для определенного делегата, операций ввода-вывода также позволяет выбрать для привязки методов протокола самостоятельно в любом классе, вам нравится, производный от NSObject, обрамления методов с ExportAttribute, а затем указав соответствующие селекторы.
При использовании этого подхода вы присвоить экземпляр вашего класса WeakDelegate свойство вместо свойства делегата. Слабый делегат предлагает гибкие возможности для класса делегата вниз по иерархии наследования разных. Давайте рассмотрим пример Xamarin.iOS, который использует делегаты сильного и слабого узлов.

### <a name="example-using-a-delegate-with-xamarinios"></a>Пример использования делегата с помощью Xamarin.iOS

Чтобы выполнить код в ответ на пользователя, коснувшись заметки в нашем примере, мы можем вывести в подкласс `MKMapViewDelegate` и присвойте экземпляр `MKMapView`в `Delegate` свойство. `MKMapViewDelegate` Протокол содержит только необязательные методы.
Таким образом, все методы являются виртуальными, привязанные к этого протокола в Xamarin.iOS `MKMapViewDelegate` класса. Когда пользователь выбирает заметки, `MKMapView` экземпляра будет отправлять `mapView:didSelectAnnotationView:` сообщение для его делегат. Для обработки таких Xamarin.iOS, нам нужно переопределить `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` метод в подкласс MKMapViewDelegate следующим образом:

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

Класс SampleMapDelegate, показанный выше реализуется как вложенный класс в контроллере, который содержит `MKMapView` экземпляра. В Objective-C вы увидите часто применять несколько протоколов, непосредственно в класс контроллера. Тем не менее так как протоколы связаны с классами в Xamarin.iOS, классы, реализующие строго типизированные делегаты обычно включаются как вложенные классы.

С помощью реализации класса делегата в месте, необходимо только создать экземпляр делегата в контроллере и назначьте его `MKMapView`в `Delegate` свойства, как показано ниже:

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

С помощью слабый делегат выполнить то же самое, вам нужно привязать метод самостоятельно в любой класс, производный от `NSObject` и назначьте его `WeakDelegate` свойство `MKMapView`. Так как `UIViewController` в конечном счете класс является производным от `NSObject` (например, каждый класс Objective-C в платформе CocoaTouch) мы можем просто реализовать метода, привязанного к `mapView:didSelectAnnotationView:` непосредственно в контроллере и назначить в контроллере `MKMapView`в `WeakDelegate`, позволяет обойтись без дополнительного вложенного класса. Приведенный ниже код демонстрирует этот подход:

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

При выполнении этого кода, что приложение ведет себя так же, как при использовании версии строго типизированных делегатов. Преимущество из этого кода в том, что слабый делегат не требуется создавать дополнительный класс, который был создан, когда мы использовали строго типизированных делегатов. Тем не менее это происходит за счет безопасность типов. Если бы пришлось допущена ошибка в селекторе, который был передан `ExportAttribute`, вы не найдете до времени выполнения.

### <a name="events-and-delegates"></a>События и делегаты

Делегаты используются для обратных вызовов в iOS точно так же как .NET использует события. Чтобы сделать iOS API-интерфейсов и способом, они используют делегаты Objective-C казаться более .NET, Xamarin.iOS предоставляет события .NET во многих местах, где используются делегаты в iOS.

Например, более ранней реализации где `MKMapViewDelegate` ответил на выбранной заметки также может быть реализована в Xamarin.iOS с помощью .NET-событие. В этом случае события должны быть определены в `MKMapView` и вызывается `DidSelectAnnotationView`. Он должен был `EventArgs` подкласс типа `MKMapViewAnnotationEventsArgs`. `View` Свойство `MKMapViewAnnotationEventsArgs` выдаст вам ссылку на представление заметки, из которого можно продолжить одной реализации необходимо было ранее, как показано здесь:

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

## <a name="summary"></a>Сводка

Эта статья описывает, как использовать события, протоколы и делегаты в Xamarin.iOS. Мы видели, как Xamarin.iOS предоставляет обычные события .NET стиля для элементов управления.
Далее мы узнали о протоколов Objective-C, включая, как они отличаются от C# интерфейсы и возможности их использования в Xamarin.iOS. Наконец мы рассмотрели Objective-C делегаты с точки зрения Xamarin.iOS. Мы видели, как Xamarin.iOS поддерживает как строго и слабо типизированные делегаты и способ привязки события .NET для делегирования методы.

## <a name="related-links"></a>Связанные ссылки

- [Протоколы, делегаты и события (пример)](https://developer.xamarin.com/samples/Protocols_Delegates_Events/)
- [Привет, iOS](~/ios/get-started/hello-ios/index.md)
- [Типы привязки Objective-C](~/ios/platform/binding-objective-c/index.md)
- [В язык программирования Objective-C](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Разработка пользовательских интерфейсов в Xcode 4](http://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Основной компетенции приложения для iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
