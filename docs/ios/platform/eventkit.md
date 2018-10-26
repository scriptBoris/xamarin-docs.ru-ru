---
title: EventKit в Xamarin.iOS
description: В этом документе описывается EventKit и способы ее использования в Xamarin.iOS. Здесь рассматриваются календарей, событиями календаря и напоминаний, отображающую классов, используемых при программировании EventKit и многое другое.
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: ea03c8b382e2de29bd20ab1d696d7abb7733e182
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120233"
---
# <a name="eventkit-in-xamarinios"></a>EventKit в Xamarin.iOS

iOS имеет два связанных с календарем приложения встроенные: приложение календаря и приложение напоминания. Это достаточно просто для того понять, как приложение календаря управляет данные календаря, но приложение напоминания является менее очевидным. Напоминания, могут иметь даты, связанных с ними того, когда они из-за, когда они завершения, и т.д. Таким образом, операций ввода-вывода сохраняет все данные календаря, будь то события календаря или напоминания в одном месте, вызывается *базы данных календаря*.

EventKit framework предоставляет способ доступа к *календарей*, *события в календаре*, и *напоминания* данные хранятся в базе данных календаря. Доступ к календарям и календарь событий был доступен с iOS 4, но доступ к напоминания впервые появился в iOS 6.

В этом руководстве мы собираемся охватывают:

-   **Основы EventKit** – это будут представлены основные части EventKit через основные классы и знакомит с возможностями их использования. В этом разделе, чтения, прежде чем браться следующей части документа. 
-   **Общие задачи** — разделе распространенные задачи должен быть краткий справочник по как выполнить общие действия, такие как; перечисление календарей, создание, сохранение и извлечение календарь событий и напоминания, а также с помощью встроенных контроллеров для Создание и изменение события календаря. В этом разделе требуется не считать от передних к задним, так как она создана для быть ссылкой для конкретных задач. 


Все задачи в этом руководстве, доступны в сопровождающем пример приложения:

 [![](eventkit-images/01.png "Экраны сопровождающий пример приложения")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>Требования

EventKit появился в iOS 4.0, но доступ к данным напоминания появился в iOS 6.0. Таким образом, чтобы сделать общей разработки EventKit, вам будет нужно выбрать по крайней мере версии 4.0 и 6.0 для напоминания.

Кроме того приложение напоминания не в симуляторе, это означает, что напоминания данные также не будут доступны, пока не добавите их сначала. Кроме того запросы на доступ только отображаются для пользователя на реальном устройстве. Таким образом EventKit разработки лучше всего тестировать на устройстве.

## <a name="event-kit-basics"></a>Общие сведения о событиях пакета

При работе с EventKit, важно иметь представление о общих классов и их использовании. Все эти классы можно найти в `EventKit` и `EventKitUI` (для `EKEventEditController`).

### <a name="eventstore"></a>EventStore

*EventStore* класс является наиболее важный класс в EventKit, так как это необходимо для выполнения любых операций в EventKit. Он может рассматриваться как постоянное хранилище, то компонент database engine, для всех данных EventKit. Из `EventStore` у вас есть доступ к календарям и события календаря в календаре, а также напоминания в приложении напоминания.

Так как `EventStore` — например база данных, он должен быть существуют достаточно долго, это означает, что он должен создаваться и уничтожаться, как можно реже в течение времени существования экземпляра приложения. На самом деле, рекомендуется после создания одного экземпляра `EventStore` в приложение, вы сохраните эту ссылку, вокруг в течение всего времени существования приложения, только если уверены, ему не нужно снова. Кроме того, все вызовы должны проходить в одном `EventStore` экземпляра. По этой причине одноэкземплярный шаблон рекомендуется использовать для хранения одного экземпляра доступны.

#### <a name="creating-an-event-store"></a>Создание событий Store

В следующем коде демонстрируется эффективный способ создать один экземпляр `EventStore` класса и сделать его доступным статически из приложения:

```csharp
public class App
{
    public static App Current {
            get { return current; }
    }
    private static App current;

    public EKEventStore EventStore {
            get { return eventStore; }
    }
    protected EKEventStore eventStore;

    static App ()
    {
            current = new App();
    }
    protected App () 
    {
            eventStore = new EKEventStore ( );
    }
}
```

Приведенный выше код использует единый шаблон для создания экземпляра `EventStore` при загрузке приложения. `EventStore` Может затем быть организован глобально в рамках приложения следующим образом:

```csharp
App.Current.EventStore;
```

Обратите внимание, что все примеры здесь использует этот шаблон, чтобы они ссылаются на `EventStore` через `App.Current.EventStore`.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Запросить доступ к календарю и данные напоминаний

Разрешение на доступ к данным через EventStore, приложение должно сначала запросить доступ к данных события календаря или данные напоминаний, в зависимости от того, какой из них нужно. Чтобы упростить эту задачу, `EventStore` предоставляет метод, называемый `RequestAccess` которого — при вызове, отобразится представление "предупреждения" для пользователя, уведомляющее о том, что приложение запрашивает доступ к данным календаря, или данные напоминаний, в зависимости от того, какие `EKEntityType`передается в него. Так как он выдает представление "предупреждения", вызов является асинхронным и будет вызывать обработчик завершения, переданный в качестве `NSAction` (или лямбда-выражение) к нему, которой будут получены два параметры; логическое выражение ли предоставлен доступ и `NSError`, который, если будет не равно null содержать все сведения об ошибках в запросе. Например следующие закодированных потребует доступа к данных события календаря и Показать просмотреть оповещение, если запрос не предоставлено.

```csharp
App.Current.EventStore.RequestAccess (EKEntityType.Event, 
    (bool granted, NSError e) => {
            if (granted)
                    //do something here
            else
                    new UIAlertView ( "Access Denied", 
"User Denied Access to Calendar Data", null,
"ok", null).Show ();
            } );
```

После запроса был предоставлен, он будет храниться, пока приложение устанавливается на устройстве и не появится предупреждение для пользователя.
Тем не менее доступ предоставляется только для типа ресурса, события календаря или предоставленные напоминания. Если приложению требуется доступ к справке, оно должно запросить оба.

Так как разрешение запоминается, он является относительно недорогим, для выполнения запроса каждый раз, поэтому рекомендуется всегда запрашивать доступ перед выполнением операции.

Кроме того, так как в потоке отдельно (без пользовательского интерфейса), вызывается обработчик завершения, все обновления в интерфейсе пользователя в обработчик завершения должен вызываться через `InvokeOnMainThread`, в противном случае будет создано исключение, и если не перехватываются, произойдет сбой приложения.

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` представляет собой перечисление, описывающее тип `EventKit` элемента или данные. Он имеет два значения: `Event` и напоминание. Он используется в ряд методов, включая `EventStore.RequestAccess` сообщить `EventKit` вида данных, чтобы получить доступ к или получения.

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* представляет календарь, который содержит группу событий календаря. Календари могут храниться во многих разных местах, таких как локально, в *iCloud*в третьей стороне расположение поставщика, таких как *Exchange Server* или *Google*и т. д. Много раз `EKCalendar` сообщающий `EventKit` где искать события или их расположение.

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* можно найти в `EventKitUI` пространства имен и — это встроенные контроллер, который может использоваться для редактирования и создания события календаря. Так же как и встроенные контроллеры камеры, `EKEventEditController` выполняет всю тяжелую работу за вас в отображения пользовательского интерфейса и обработка сохранение.

### <a name="ekevent"></a>EKEvent

 *EKEvent* представляет события календаря. Оба `EKEvent` и `EKReminder` наследовать от `EKCalendarItem` и иметь поля, такие как `Title`, `Notes`, и т. д.

### <a name="ekreminder"></a>EKReminder

 *EKReminder* представляет элемент напоминание.

### <a name="ekspan"></a>EKSpan

*EKSpan* является перечислением, описывающий диапазон событий при изменении события, которые можно повторять и имеет два значения: *данный* и *FutureEvents*. `ThisEvent` означает, что любые изменения только произойдет определенное событие в серии, на который ссылается, тогда как `FutureEvents` будет влиять на это событие и все будущие повторения.

## <a name="tasks"></a>Задачи

Для удобства использования EventKit был разбит на общие задачи, описанные в следующих разделах.

### <a name="enumerate-calendars"></a>Перечислить календарей

Чтобы перечислить календари, которые пользователь настроил на устройстве, вызовите `GetCalendars` на `EventStore` и передать этот тип календарей (напоминания или событий), которые вы хотите получать:

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Добавить или изменить событие, используя встроенные контроллер

*EKEventEditViewController* выполняет множество часть тяжелой работы для вас, если вы хотите создать или изменить событие с тот же пользовательский Интерфейс, которые отображаются для пользователя при работе с приложением календаря:

 [![](eventkit-images/02.png "Пользовательский Интерфейс, который предоставляется пользователю при работе с приложением календаря")](eventkit-images/02.png#lightbox)

Чтобы использовать его, необходимо объявить ее как переменную уровня класса, чтобы его не получить сборщиком мусора, если он объявлен внутри метода:

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

Затем, чтобы запустить его: создать его экземпляр, присвойте ему ссылку на `EventStore`, привязывания *EKEventEditViewDelegate* делегировать и отобразите его с помощью `PresentViewController`:

```csharp
EventKitUI.EKEventEditViewController eventController = 
        new EventKitUI.EKEventEditViewController ();

// set the controller's event store - it needs to know where/how to save the event
eventController.EventStore = App.Current.EventStore;

// wire up a delegate to handle events from the controller
eventControllerDelegate = new CreateEventEditViewDelegate ( eventController );
eventController.EditViewDelegate = eventControllerDelegate;

// show the event controller
PresentViewController ( eventController, true, null );
```

При необходимости Если вы хотите предварительно заполнить события, можно создать совершенно новое событие (как показано ниже) или сохраненного события можно получить:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and exercise!";
newEvent.Notes = "This is your reminder to go and exercise for 30 minutes.”;
```

Если вы хотите предварительно заполнить пользовательского интерфейса, убедитесь, что свойства события на контроллере:

```csharp
eventController.Event = newEvent;
```

Использование существующего события, см. в разделе *получение событий по Идентификатору* разделе позже.

Делегат должен переопределить `Completed` метод, который вызывается с помощью контроллера, когда пользователь завершил работу с диалоговым окном:

```csharp
protected class CreateEventEditViewDelegate : EventKitUI.EKEventEditViewDelegate
{
        // we need to keep a reference to the controller so we can dismiss it
        protected EventKitUI.EKEventEditViewController eventController;

        public CreateEventEditViewDelegate (EventKitUI.EKEventEditViewController eventController)
        {
                // save our controller reference
                this.eventController = eventController;
        }

        // completed is called when a user eith
        public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
        {
                eventController.DismissViewController (true, null);
                }
        }
}
```

При необходимости в делегате, можно проверить *действие* в `Completed` метод, чтобы изменить событие и повторно сохранить или выполнять другие действия, в том случае, если отменен и:

```csharp
public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
{
        eventController.DismissViewController (true, null);

        switch ( action ) {

        case EKEventEditViewAction.Canceled:
                break;
        case EKEventEditViewAction.Deleted:
                break;
        case EKEventEditViewAction.Saved:
                // if you wanted to modify the event you could do so here,
// and then save:
                //App.Current.EventStore.SaveEvent ( controller.Event, )
                break;
        }
}
```

### <a name="creating-an-event-programmatically"></a>Создание события программным способом

Чтобы создать событие в коде, используйте *FromStore* фабричный метод на `EKEvent` и укажите все данные на нем:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and do some exercise!";
newEvent.Notes = "This is your motivational event to go and do 30 minutes of exercise. Super important. Do this.";
```

Необходимо задать календарь, который сохранен в события, но если у вас нет предпочтений, можно использовать значение по умолчанию:

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Чтобы сохранить событие, вызовите *SaveEvent* метод `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

После сохранения *EventIdentifier* с уникальным идентификатором, который может использоваться для извлечения события позднее будет обновлено свойство:

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` — Это строка в формате GUID.

### <a name="create-a-reminder-programmatically"></a>Программное создание напоминание

Напоминание в коде создается так же как при создании события календаря:

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

Чтобы сохранить, вызовите *SaveReminder* метод `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>Получение события по Идентификатору

Получить событие, его идентификатор, использовать *EventFromIdentifier* метод `EventStore` и передать его `EventIdentifier` , было извлечено из события:

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

Для событий, имеется два свойства идентификатора, но `EventIdentifier` является единственным, подходящий для этого.

### <a name="retrieving-a-reminder-by-id"></a>Получение напоминание по Идентификатору

Чтобы получить напоминание, используйте *GetCalendarItem* метод `EventStore` и передать его *CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

Так как `GetCalendarItem` возвращает `EKCalendarItem`, он должен быть приведен к `EKReminder` Если вам нужно получить доступ к данным напоминание или использовать экземпляр в качестве `EKReminder` позже.

Не используйте `GetCalendarItem` событий календаря, как и на момент написания статьи, он не работает.

### <a name="deleting-an-event"></a>Удаление события

Чтобы удалить события календаря, вызовите *RemoveEvent* на вашей `EventStore` и передать ссылку на событие и соответствующий `EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

Примечание тем не менее, после удаления события, ссылку на событий будет `null`.

### <a name="deleting-a-reminder"></a>Удалить напоминание

Чтобы удалить напоминание, вызовите *RemoveReminder* на `EventStore` и передать ссылку на напоминание:

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Обратите внимание, что в приведенном выше коде приведение к `EKReminder`, так как `GetCalendarItem` использовался для его получения

### <a name="searching-for-events"></a>Поиск событий

Чтобы найти события в календаре, необходимо создать *NSPredicate* объекта с помощью *PredicateForEvents* метод `EventStore`. `NSPredicate` Запрос является запросом объект данных, операций ввода-вывода использует для поиска совпадений:

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

После того, как `NSPredicate`, использовать *EventsMatching* метод `EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Обратите внимание, что синхронные (блокировки) и запросы может занять много времени, в зависимости от запроса, поэтому может потребоваться запустить новый поток или задачу, чтобы сделать это.

### <a name="searching-for-reminders"></a>Поиск напоминания

Поиск напоминания похож на события; она требует предикат, но вызов уже выполняется асинхронно, чтобы не беспокоиться о блокировке потока:

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>Сводка

В этом документе написал обзор важные моменты EventKit framework, а также ряд наиболее распространенных задач. Тем не менее, очень большой и мощные и включает возможности, которые еще не были введены, такие как EventKit framework: пакетные обновления, Настройка оповещений, Настройка повторения на события, регистрация и прослушивание изменений в базе данных календаря Настройка Геозон и многое другое.  Дополнительные сведения см. в разделе Apple [календаря и руководство по программированию напоминания](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html).


## <a name="related-links"></a>Связанные ссылки

- [Календари (пример)](https://developer.xamarin.com/samples/monotouch/Calendars/)
- [Введение в iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Общие сведения о календарях и напоминаний](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
