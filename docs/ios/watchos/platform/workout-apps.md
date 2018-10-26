---
title: watchOS тренировок приложений в Xamarin
description: В этой статье рассматриваются усовершенствования Apple сделала для тренировок приложений в watchOS 3, а также способы их реализации в Xamarin.
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: d755160043191f93247fd09e99f23eb85831fa8b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113902"
---
# <a name="watchos-workout-apps-in-xamarin"></a>watchOS тренировок приложений в Xamarin

_В этой статье рассматриваются усовершенствования Apple сделала для тренировок приложений в watchOS 3, а также способы их реализации в Xamarin._


Новое для watchOS 3, тренировки связанные приложения имеют возможность работать в фоновом режиме на Apple Watch и получить доступ к данным HealthKit. IOS 10 на основе их родительское приложение также имеет возможность запустить приложение на основе watchOS 3 без вмешательства пользователя.

Будут подробно рассмотрены следующие темы:

## <a name="about-workout-apps"></a>О приложениях для организации тренировок

Пользователи приложений пригодности и тренировок могут быть высокой выделенный, выделяя несколько часов дня для поставленных целей здравоохранения и. Как следствие они ожидают быстрые, простые в использовании приложений, которые точно сбора и отображения данных и легко интегрировать работоспособности Apple.

Хорошо спроектированные приложения пригодности или тренировок помогает пользователям, которые их действия, чтобы достичь своих целей в отношении пригодности диаграммы. С помощью Apple Watch, применимости и тренировок приложения имеют мгновенный доступ к частота пульса калория темп работ и действий обнаружения.

[![](workout-apps-images/workout01.png "Пример приложения пригодности и тренировок")](workout-apps-images/workout01.png#lightbox)

В watchOS 3, _под управлением фона_ тренировок дает связанных приложений возможность работать в фоновом режиме на Apple Watch и получить доступ к данным HealthKit.

В этом документе будет появился компонент выполнение в фоновом режиме, охватывает жизненный цикл приложения для тренировок и показать, как создать приложение для организации тренировок могут принимать участие пользователя _действия колец_ на Apple Watch.

## <a name="about-workout-sessions"></a>О сеансах тренировок

Является основой каждого приложения для тренировок _сеанса тренировок_ (`HKWorkoutSession`), пользователь может запустить и остановить. API для организации тренировок сеанса прост в реализации и предоставляет ряд преимуществ, в приложении тренировки, такие как:

- Методологии Motion и калория записи обнаружения, в зависимости от типа действия.
- Автоматическое вклад для кругов действия пользователя.
- В сеансе, приложение будет автоматически отображаться каждый раз, когда пользователь в рабочее состояние устройства (либо путем вызова их запястья или взаимодействия с Apple Watch).

## <a name="about-background-running"></a>О запуске фона

Как уже говорилось выше, в watchOS 3 приложения для тренировок можно задать для запуска в фоновом режиме. С помощью фона под управлением приложения для тренировок может обрабатывать данные датчиков Apple Watch во время выполнения в фоновом режиме. Например приложение может продолжать отслеживать пользователя пульс, несмотря на то, что он больше не отображается на экране.

Выполнение в фоновом режиме также предоставляет возможность пользователь получает отправки отзывов в любое время во время активного сеанса тренировки, например, отправку haptic оповещение, чтобы информировать пользователей об их текущее состояние.

Кроме того запуск в фоновом режиме позволяет приложению быстро обновить пользовательский интерфейс, поэтому пользователь имеет самые последние данные, если они быстро взглянуть на их Apple Watch.

Для поддержания высокого уровня производительности на Apple Watch, приложение просмотра с помощью фонового выполнения следует ограничить объем фоновой работы в целях экономии батареи. Если приложение использует слишком много ресурсов ЦП в фоновом режиме, он может получить приостановлено watchOS.

### <a name="enabling-background-running"></a>Включение фона под управлением

Чтобы включить выполнение в фоновом режиме, сделайте следующее:

1. В **обозревателе решений**, дважды щелкните модуль Watch сопутствующее iPhone приложение `Info.plist` файл, чтобы открыть его для редактирования.
2. Переключиться в режим **источника** представления: 

    [![](workout-apps-images/plist01.png "В представлении источника")](workout-apps-images/plist01.png#lightbox)
3. Добавьте новый ключ с именем `WKBackgroundModes` и задайте **тип** для `Array`: 

    [![](workout-apps-images/plist02.png "Добавьте новый ключ с именем WKBackgroundModes")](workout-apps-images/plist02.png#lightbox)
4. Добавить новый элемент в массив с **тип** из `String` и значение `workout-processing`: 

    [![](workout-apps-images/plist03.png "Добавить новый элемент в массив с строковый тип и значение обработки для организации тренировок")](workout-apps-images/plist03.png#lightbox)
5. Сохраните изменения в файле.

## <a name="starting-a-workout-session"></a>Запуск сеанса тренировок

Существует три основных этапа запуска сеанса тренировок.

[![](workout-apps-images/workout02.png "Три основных этапа запуска сеанса тренировок")](workout-apps-images/workout02.png#lightbox)

1. Приложение должно запросить авторизацию для доступа к данным в HealthKit.
2. Создайте объект конфигурации для организации тренировок для типа запуска для тренировок.
3. Создайте и запустите сеанс тренировки, с помощью созданной конфигурации тренировок.

### <a name="requesting-authorization"></a>Запрашивает авторизацию

Перед приложения могут обращаться к данным HealthKit пользователя, его необходимо запросить и получить авторизацию от пользователя. В зависимости от характера приложения для тренировок может быть следующих типов запросов:

- Разрешение на запись данных:
    - Тренировок
- Разрешение на чтение данных:
    - Энергии, потребляемой
    - расстояние
    - Частота пульса  

Прежде чем приложение может запросить авторизацию, его необходимо настроить для доступа к HealthKit.

Выполните следующие действия:

1. В **обозревателе решений** дважды щелкните файл `Entitlements.plist`, чтобы открыть его для редактирования.
2. Прокрутите вниз и проверьте **включить HealthKit**: 

    [![](workout-apps-images/auth01.png "Флажок Включить HealthKit")](workout-apps-images/auth01.png#lightbox)
3. Сохраните изменения в файле.
4. Следуйте инструкциям в [явный идентификатор приложения и профиль подготовки](~/ios/platform/healthkit.md) и [связывание идентификатор приложения и подготовки профиля с помощью Xamarin.iOS приложений](~/ios/platform/healthkit.md) разделы [введение HealthKit](~/ios/platform/healthkit.md) статью, чтобы правильно подготовить приложение.
5. Наконец, выполните инструкции из статьи [программирования Kit работоспособности](~/ios/platform/healthkit.md) и [запрашивает разрешение от пользователя](~/ios/platform/healthkit.md) разделы [введение в HealthKit](~/ios/platform/healthkit.md) статьи на запрос разрешение на доступ к хранилищу данных пользователя HealthKit.

### <a name="setting-the-workout-configuration"></a>Настройка конфигурации для организации тренировок

Тренировки сеансы создаются с использованием объекта для тренировок конфигурации (`HKWorkoutConfiguration`), указывающий тип тренировок (такие как `HKWorkoutActivityType.Running`) и расположение для организации тренировок (такие как `HKWorkoutSessionLocationType.Outdoor`):

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>Создание сеанса делегат для организации тренировок 

Для обработки событий, которые могут возникнуть во время сеанса тренировки, приложение нужно будет создать экземпляр делегата сеанса тренировок. Добавьте новый класс в проект и его за пределами класса базового `HKWorkoutSessionDelegate` класса. Пример outdoor выполнения он может выглядеть следующим образом:

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }
        #endregion
    }
}
```

Этот класс создает несколько событий, которые будет вызываться как состояние сеанса для организации тренировок изменений (`DidChangeToState`) и при сбое тренировок сеанса (`DidFail`). 

### <a name="creating-a-workout-session"></a>Создание сеанса для организации тренировок

С помощью конфигурации тренировки и делегат сеанса тренировок созданной ранее для создания нового сеанса тренировки и запуска HealthKit хранилище пользователя по умолчанию:

```csharp
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

private void StartOutdoorRun ()
{
    // Create a workout configuration
    var configuration = new HKWorkoutConfiguration () {
        ActivityType = HKWorkoutActivityType.Running,
        LocationType = HKWorkoutSessionLocationType.Outdoor
    };

    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (configuration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

Если приложение запускает этот сеанс тренировки и переключении обратно в их циферблате, маленький зеленый значок «running man» отображается выше начертание шрифта.

[![](workout-apps-images/workout03.png "Маленький зеленый работающей man значок отображенный выше начертание шрифта")](workout-apps-images/workout03.png#lightbox)

Если пользователь касается этот значок, он будет перенаправлен обратно в приложение.

## <a name="data-collection-and-control"></a>Сбор данных и управления

После сеанса тренировок настроен и запущен, приложение нужно будет собирать данные о сеансе (например, частота пульса пользователя) и управления состоянием сеанса:

[![](workout-apps-images/workout04.png "Сбор данных и схема управления")](workout-apps-images/workout04.png#lightbox)

1. **Рассмотрение примеров** -приложению потребуется для получения сведений из HealthKit, который будет оказано и отображается для пользователя.
2. **Наблюдение за событиями** -приложению потребуется реагировать на события, создаваемые HealthKit или с помощью пользовательского интерфейса приложения (например, пользователь, приостановка тренировок).
3. **Переход в состояние выполнения** -сеанс был запущен и выполняется в данный момент.
4. **Введите приостановлено** -пользователь приостановлена в текущий сеанс тренировки и перезапустить его позже. Пользователь может переключаться между запущен и приостановленного состояния несколько раз в одном сеансе тренировок.
5. **Завершение сеанса тренировок** — в любой момент пользователь может завершить сеанс тренировки или он может срок действия и завершить сам по себе, если он был отслеживаемых тренировок (например, запуск двух миль).

Последним шагом является сохранение результатов сеанса тренировок хранилище данных HealthKit пользователя.

### <a name="observing-healthkit-samples"></a>Рассмотрение примеров HealthKit

Приложение нужно будет сначала открыть _запроса объектов привязки_ для каждого из данных HealthKit точек, интересующие его, такие как частота пульса или активных энергии записи. Для каждой точки данных, повлиявшую обработчика обновлений должны создаваться собирать новые данные, отправляемые в приложение.

Из этих точек данных приложение может накапливаться итоги (например, общее расстояние выполнения) и обновить его пользовательский интерфейс при необходимости. Кроме того приложение может уведомлять пользователей о их достижения конкретной цели или достижения, такие как завершение Далее мили запуска.

Взгляните на следующий образец кода:

```csharp
private void ObserveHealthKitSamples ()
{
    // Get the starting date of the required samples
    var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

    // Get data from the local device
    var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
    var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

    // Assemble compound predicate
    var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

    // Get ActiveEnergyBurned
    var queryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
        // Valid?
        if (error == null) {
            // Yes, process all returned samples
            foreach (HKSample sample in addedObjects) {
                var quantitySample = sample as HKQuantitySample;
                ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);
            }
            
            // Update User Interface
            ...
        }
    });

    // Start Query
    HealthStore.ExecuteQuery (queryActiveEnergyBurned);
                                          
}
```

Он создает предикат, который необходимо задать дату начала, в которой необходимо получить данные по использованию `GetPredicateForSamples` метод. Он создает набор устройств, чтобы получить сведения о HealthKit из с помощью `GetPredicateForObjectsFromDevices` метод, в данном случае только локальный Apple Watch (`HKDevice.LocalDevice`). Два предиката объединяются в составной предиката (`NSCompoundPredicate`) с помощью `CreateAndPredicate` метод.

Новый `HKAnchoredObjectQuery` создается для требуемого точки данных (в данном случае `HKQuantityTypeIdentifier.ActiveEnergyBurned` Active энергии записи точки данных), отсутствие ограничений на объем данных, возвращаемых (`HKSampleQuery.NoLimit`) и обработчика обновлений определяется для обработки данных выполняется возврат к приложению из HealthKit. 

Обработчик обновлений будет вызываться каждый раз, когда новые данные доставляется в приложение для точки данных. Если ошибка не возвращается, приложение может безопасно считывать данные, внести все необходимые вычисления и обновлять его пользовательский Интерфейс при необходимости.

Код в цикле все образцы (`HKSample`) возвращаются в `addedObjects` массива и приводит их количество выборки (`HKQuantitySample`). Затем он получает значение типа double образце в качестве джоуль (`HKUnit.Joule`) и аккумулирует его в нарастающий итог active энергии, потребляемой для тренировки и обновляет пользовательский интерфейс.

### <a name="achieved-goal-notification"></a>Уведомление о достигаемой цель

Как упоминалось выше когда пользователь достигает цели в приложении для организации тренировок (например, завершение работы первого мили запуска), он может отправить обратной связи haptic пользователю с помощью Taptic ядра. Приложения должны также обновить его пользовательского интерфейса на этом этапе, так как пользователь скорее всего будет повышать их запястье, чтобы просмотреть событие, которое запрос обратной связи.

Чтобы воспроизвести связи haptic, используйте следующий код:

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>Наблюдение за событиями

События, отметки времени, приложение может использовать для выделения определенных точках во время тренировки пользователя. Некоторые события будут непосредственно приложением и сохранить его в тренировки и некоторые события будут создаваться автоматически по HealthKit.

Чтобы увидеть события, созданные HealthKit, переопределит приложение `DidGenerateEvent` метод `HKWorkoutSessionDelegate`:

```csharp
using System.Collections.Generic;
...

public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Save HealthKit generated event
    WorkoutEvents.Add (@event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Lap:
        break;
    case HKWorkoutEventType.Marker:
        break;
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

В watchOS 3 Apple добавлены следующие новые типы событий:

- `HKWorkoutEventType.Lap` -Предназначены для событий, которые разбить тренировок на одинаковом расстоянии частей. Например для пометки одного краткая информация о дорожке во время выполнения.
- `HKWorkoutEventType.Marker` -Для произвольных Достопримечательности находятся в пределах тренировок. Например достижение определенной точке на маршрут стационарные выполнения.

Эти новые типы можно созданные приложением и хранятся в тренировок для дальнейшего использования при создании диаграммы и статистические данные.

Создание событий маркера, сделайте следующее:

```csharp
using System.Collections.Generic;
...

public float MilesRun { get; set; }
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public void ReachedNextMile ()
{
    // Create and save marker event
    var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
    WorkoutEvents.Add (markerEvent);

    // Notify user
    NotifyUserOfReachedMileGoal (++MilesRun);
}
```

Этот код создает новый экземпляр класса событий маркера (`HKWorkoutEvent`) и сохраняет его в частную коллекцию событий (которые будут записаны позже в сеанс тренировки) и уведомляет пользователя через haptics события.

### <a name="pausing-and-resuming-workouts"></a>Приостановка и возобновление тренировок

В любой момент сеанс тренировки пользователь может временно приостановить тренировки и возобновить ее позже. Например они могут приостановить выполнения внутренний вызов важные и возобновление выполнения после завершения вызова.

Пользовательском Интерфейсе приложения должен предоставить возможность приостанавливать и возобновлять тренировок (посредством вызова HealthKit), таким образом, Apple Watch больше места, power и данных приостановленная пользователь свои действия. Кроме того приложение должно игнорировать все новые точки данных, которые могут быть получены при тренировок сеанс находится в приостановленном состоянии.

Для приостановки и возобновления вызовы, создавая Приостановка и возобновление событий будет отвечать HealthKit. После приостановки сеанса тренировок не новые события или данные будут отправляться в приложение с HealthKit до возобновления сеанса.

Используйте следующий код, приостанавливать и возобновлять сеанс тренировки:

```csharp
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public HKWorkoutSession WorkoutSession { get; set;}
...

public void PauseWorkout ()
{
    // Pause the current workout
    HealthStore.PauseWorkoutSession (WorkoutSession);
}

public void ResumeWorkout ()
{
    // Pause the current workout
    HealthStore.ResumeWorkoutSession (WorkoutSession);
}
```

Приостановка и возобновление события, которые будут созданы из HealthKit можно обрабатывать путем переопределения `DidGenerateEvent` метод `HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);

    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

### <a name="motion-events"></a>События движения

Также новые для watchOS 3, являются движения, приостановлена (`HKWorkoutEventType.MotionPaused`) и возобновление движения (`HKWorkoutEventType.MotionResumed`) события. Эти события вызываются автоматически HealthKit во время выполнения для организации тренировок когда пользователь запускает и останавливает перемещение.

Когда приложение получает событие приостановки движения, его следует остановить сбор данных, пока пользователь не возобновит движения и получении события возобновляет движения. Приложение не приостановите тренировок сеанса в ответ на событие приостановки движения.

> [!IMPORTANT]
> Движения приостановки и возобновления движения события поддерживаются только для типа действия RunningWorkout (`HKWorkoutActivityType.Running`).

Опять же, эти события могут обрабатываться в соответствии с путем переопределения `DidGenerateEvent` метод `HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    }
}

```

## <a name="ending-and-saving-the-workout-session"></a>Завершение и сохранение сеанса тренировок

По завершении их тренировок пользователь приложения необходимо завершить текущий сеанс тренировки и сохраните его в базу данных HealthKit. Автоматически тренировок сохранены HealthKit будет отображаться в списке действий тренировок.

Новое в iOS 10, включает список список действий для организации тренировок на iPhone пользователя также. Поэтому даже если Apple Watch нет рядом, тренировки откроется на телефоне.

Тренировок, включающие примеры энергии будет обновление кольцо перемещение пользователя в приложении действия, чтобы сторонние приложения теперь могут способствовать ежедневного перемещения целей пользователя.

Для окончания и сохранение сеанса тренировок требуются следующие действия:

[![](workout-apps-images/workout05.png "Завершение и сохранении диаграммы сеанса тренировок")](workout-apps-images/workout05.png#lightbox)

1. Во-первых приложение потребуется для завершения сеанса для тренировок.
2. Сеанс тренировки сохраняется HealthKit.
3. Добавьте любые примеры (например, энергии, потребляемой или расстояние) сохраненный сеанс тренировок.

### <a name="ending-the-session"></a>Завершение сеанса

Чтобы завершить сеанс тренировки, вызовите `EndWorkoutSession` метод `HKHealthStore` передавая `HKWorkoutSession`:

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
...

public void EndOutdoorRun ()
{
    // End the current workout session
    HealthStore.EndWorkoutSession (WorkoutSession);
}
```

Это приведет к сбросу датчиков устройств в своих обычный режим. По завершении HealthKit конца тренировки, он получит обратный вызов, `DidChangeToState` метод `HKWorkoutSessionDelegate`:

```csharp
public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
{
    // Take action based on the change in state
    switch (toState) {
    ...
    case HKWorkoutSessionState.Ended:
        StopObservingHealthKitSamples ();
        RaiseEnded ();
        break;
    }

}
```

### <a name="saving-the-session"></a>Сохранение сеанса

После окончания сеанса тренировок приложения его нужно будет создать для организации тренировок (`HKWorkout`) и сохраните его (вместе с событиями) в хранилище данных HealthKit (`HKHealthStore`):

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
public float MilesRun { get; set; }
public double ActiveEnergyBurned { get; set;}
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

private void SaveWorkoutSession ()
{
    // Build required workout quantities 
    var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
    var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

    // Create any required metadata
    var metadata = new NSMutableDictionary ();
    metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

    // Create workout
    var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                    WorkoutSession.StartDate, 
                                    NSDate.Now, 
                                    WorkoutEvents.ToArray (), 
                                    energyBurned, 
                                    distance, 
                                    metadata);

    // Save to HealthKit
    HealthStore.SaveObject (workout, (successful, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (successful) {

            }
        } else {
            // Report error
        }
    });

}
```

Этот код создает требуется общий объем энергии, потребляемой и расстояние для тренировок как `HKQuantity` объектов. Создается словарь метаданные, определяющие тренировки и указывается расположение тренировок:

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

Новый `HKWorkout` создается объект с тем же `HKWorkoutActivityType` как `HKWorkoutSession`, даты начала и окончания, список (собранных из вышеперечисленных) событий, энергии, потребляемой в, общее расстояние и словарь метаданных. Этот объект сохраняется Store работоспособности и обработки ошибок.  

### <a name="adding-samples"></a>Добавление примеры

Когда приложение сохраняет набор примеров для тренировки, HealthKit создает соединение между примерами и тренировки, сам таким образом, чтобы приложение можно запросить HealthKit в дальнейшем для всех образцов, относящихся к заданной тренировок. Используя эту информацию, приложение может Создание диаграмм на основе данных для организации тренировок и отобразив их на временной шкале тренировок.

Приложение для пополнения кольцо переместить приложения Activity он должен содержать примеры энергии с сохраненного тренировок. Кроме того итоги для расстояния и энергии должно соответствовать сумму всех образцов, приложение связывает с сохраненного тренировок.

Чтобы добавить образцы сохраненного тренировки, сделайте следующее:

```csharp
using System.Collections.Generic;
using WatchKit;
using HealthKit;
...

public HKHealthStore HealthStore { get; private set; }
public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
...


private void SaveWorkoutSamples (HKWorkout workout)
{
    // Add samples to saved workout
    HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (success) {

            }
        } else {
            // Report error
        }
    });
}
```

При необходимости приложение может вычислить и создать определенное подмножество образцы или только один мегабайт образец (охватывающие весь диапазон тренировок), который затем связывается с сохраненного тренировок.

## <a name="workouts-and-ios-10"></a>IOS 10 и тренировок

Каждое приложение тренировки watchOS 3 имеет приложения на основе тренировки iOS 10 родительского и опыта работы с iOS 10, это приложение iOS может использоваться для запуска тренировки, который будет размещать Apple Watch в режиме тренировки (без вмешательства пользователя) и запустите приложение, watchOS в режиме выполнения фоновой (см. [инастроеко запущен фоновый](#About-Background-Running) выше для получения дополнительных сведений).

Пока выполняется приложение watchOS, его можно использовать WatchConnectivity для обмена данными с приложением iOS, родительский и.

Рассмотрим, как работает этот процесс:

[![](workout-apps-images/workout06.png "iPhone и схема связей Apple Watch")](workout-apps-images/workout06.png#lightbox)

1. Создает приложение для iPhone `HKWorkoutConfiguration` и устанавливает для организации тренировок тип и расположение.
2. `HKWorkoutConfiguration` Объект отправляется версии приложения для Apple Watch и, если он еще не запущен, он запускается системой.
3. С помощью переданного в конфигурации тренировки, приложение watchOS 3 начинает новый сеанс тренировки (`HKWorkoutSession`).

> [!IMPORTANT]
> Чтобы iPhone в родительское приложение запустить тренировок на Apple Watch приложение watchOS 3 должно иметь фона запуска включены. См. в разделе [Включение фона под управлением](#Enabling-Background-Running) выше для получения дополнительных сведений.

Этот процесс очень похож на процесс запуска сеанса тренировок в приложении watchOS 3 непосредственно. На iPhone используйте следующий код:

```csharp
using System;
using HealthKit;
using WatchConnectivity;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
#endregion
...

private void StartOutdoorRun ()
{
    // Can the app communicate with the watchOS version of the app?
    if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
        // Create a workout configuration
        var configuration = new HKWorkoutConfiguration () {
            ActivityType = HKWorkoutActivityType.Running,
            LocationType = HKWorkoutSessionLocationType.Outdoor
        };

        // Start watch app
        HealthStore.StartWatchApp (configuration, (success, error) => {
            // Handle any errors
            if (error == null) {
                // Was the save successful
                if (success) {
                    ...
                }
            } else {
                // Report error
                ...
            }
        });
    }
}
```

Этот код гарантирует, что установлена версия watchOS приложения и версии для iPhone можно сначала подключиться к нему:

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    ...
}
```

Затем он создает `HKWorkoutConfiguration` обычным образом и использует `StartWatchApp` метод `HKHealthStore` для отправки его в Apple Watch и запустить приложение и сеанс тренировок.

И в приложении для Apple watch операционной системы, используйте следующий код в `WKExtensionDelegate`:

```csharp
using WatchKit;
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...


public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
{
    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

Он принимает `HKWorkoutConfiguration` и создает новый `HKWorkoutSession` и присоединяет экземпляр пользовательского `HKWorkoutSessionDelegate`. Для организации тренировок сеанс запущен от пользователя HealthKit Health Store.

## <a name="bringing-all-the-pieces-together"></a>Объединяя все фрагменты

Принимая все сведения, представленные в этом документе, в приложении на основе тренировок watchOS 3, а также его родительское приложение на основе тренировок iOS 10 может включать следующие части:

1. **iOS 10 `ViewController.cs`**  -обрабатывает начиная сеанс подключения контрольных значений и тренировок на Apple Watch.
2. **watchOS 3 `ExtensionDelegate.cs`**  -обрабатывает watchOS 3 версии приложения для тренировок.
3. **watchOS 3 `OutdoorRunDelegate.cs`**  — пользовательское `HKWorkoutSessionDelegate` для обработки событий для тренировок.

> [!IMPORTANT]
> Код, показанный в следующих разделах включает только компоненты, необходимые для реализации новых инструментов, возможности, предоставляемые в watchOS 3 приложения для тренировок. Все вспомогательные код и код для представления и обновить пользовательский Интерфейс не включено, но можно легко создать, следуя документации watchOS.<p/>



### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController.cs` Файл в iOS 10 родительского версию приложения для тренировок будет содержать следующий код:

```csharp
using System;
using HealthKit;
using UIKit;
using WatchConnectivity;

namespace MonkeyWorkout
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
        public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Private Methods
        private void InitializeWatchConnectivity ()
        {
            // Is Watch Connectivity supported?
            if (!WCSession.IsSupported) {
                // No, abort
                return;
            }

            // Is the session already active?
            if (ConnectivitySession.ActivationState != WCSessionActivationState.Activated) {
                // No, start session
                ConnectivitySession.ActivateSession ();
            }
        }

        private void StartOutdoorRun ()
        {
            // Can the app communicate with the watchOS version of the app?
            if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
                // Create a workout configuration
                var configuration = new HKWorkoutConfiguration () {
                    ActivityType = HKWorkoutActivityType.Running,
                    LocationType = HKWorkoutSessionLocationType.Outdoor
                };

                // Start watch app
                HealthStore.StartWatchApp (configuration, (success, error) => {
                    // Handle any errors
                    if (error == null) {
                        // Was the save successful
                        if (success) {
                            ...
                        }
                    } else {
                        // Report error
                        ...
                    }
                });
            }
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Start Watch Connectivity
            InitializeWatchConnectivity ();
        }
        #endregion
    }
}
```

### <a name="extensiondelegatecs"></a>ExtensionDelegate.cs

`ExtensionDelegate.cs` Файл в watchOS 3 версии приложения для тренировок будет содержать следующий код:

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
        public OutdoorRunDelegate RunDelegate { get; set; }
        #endregion

        #region Constructors
        public ExtensionDelegate ()
        {
            
        }
        #endregion

        #region Private Methods
        private void StartWorkoutSession (HKWorkoutConfiguration workoutConfiguration)
        {
            // Create workout session
            // Start workout session
            NSError error = null;
            var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

            // Successful?
            if (error != null) {
                // Report error to user and return
                return;
            }

            // Create workout session delegate and wire-up events
            RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

            RunDelegate.Failed += () => {
                // Handle the session failing
                ...
            };

            RunDelegate.Paused += () => {
                // Handle the session being paused
                ...
            };

            RunDelegate.Running += () => {
                // Handle the session running
                ...
            };

            RunDelegate.Ended += () => {
                // Handle the session ending
                ...
            };
            
            RunDelegate.ReachedMileGoal += (miles) => {
                // Handle the reaching a session goal
                ...
            };

            RunDelegate.HealthKitSamplesUpdated += () => {
                // Update UI as required
                ...
            };

            // Start session
            HealthStore.StartWorkoutSession (workoutSession);
        }

        private void StartOutdoorRun ()
        {
            // Create a workout configuration
            var workoutConfiguration = new HKWorkoutConfiguration () {
                ActivityType = HKWorkoutActivityType.Running,
                LocationType = HKWorkoutSessionLocationType.Outdoor
            };

            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion

        #region Override Methods
        public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
        {
            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion
    }
}
```

### <a name="outdoorrundelegatecs"></a>OutdoorRunDelegate.cs

`OutdoorRunDelegate.cs` Файл в watchOS 3 версии приложения для тренировок будет содержать следующий код:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Private Variables
        private HKAnchoredObjectQuery QueryActiveEnergyBurned;
        #endregion

        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        public float MilesRun { get; set; }
        public double ActiveEnergyBurned { get; set;}
        public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
        public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;

        }
        #endregion

        #region Private Methods
        private void ObserveHealthKitSamples ()
        {
            // Get the starting date of the required samples
            var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

            // Get data from the local device
            var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
            var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

            // Assemble compound predicate
            var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

            // Get ActiveEnergyBurned
            QueryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
                // Valid?
                if (error == null) {
                    // Yes, process all returned samples
                    foreach (HKSample sample in addedObjects) {
                        // Accumulate totals
                        var quantitySample = sample as HKQuantitySample;
                        ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);

                        // Save samples
                        WorkoutSamples.Add (sample);
                    }

                    // Inform caller
                    RaiseHealthKitSamplesUpdated ();
                }
            });

            // Start Query
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
                                                  
        }

        private void StopObservingHealthKitSamples ()
        {
            // Stop query
            HealthStore.StopQuery (QueryActiveEnergyBurned);
        }

        private void ResumeObservingHealthkitSamples ()
        {
            // Resume current queries 
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
        }

        private void NotifyUserOfReachedMileGoal (float miles)
        {
            // Play haptic feedback
            WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);

            // Raise event
            RaiseReachedMileGoal (miles);
        }

        private void SaveWorkoutSession ()
        {
            // Build required workout quantities
            var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
            var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

            // Create any required metadata
            var metadata = new NSMutableDictionary ();
            metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

            // Create workout
            var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                            WorkoutSession.StartDate, 
                                            NSDate.Now, 
                                            WorkoutEvents.ToArray (), 
                                            energyBurned, 
                                            distance, 
                                            metadata);

            // Save to HealthKit
            HealthStore.SaveObject (workout, (successful, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (successful) {
                        // Add samples to workout
                        SaveWorkoutSamples (workout);
                    }
                } else {
                    // Report error
                    ...
                }
            });

        }

        private void SaveWorkoutSamples (HKWorkout workout)
        {
            // Add samples to saved workout
            HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (success) {
                        ...
                    }
                } else {
                    // Report error
                    ...
                }
            });
        }
        #endregion

        #region Public Methods
        public void PauseWorkout ()
        {
            // Pause the current workout
            HealthStore.PauseWorkoutSession (WorkoutSession);
        }

        public void ResumeWorkout ()
        {
            // Pause the current workout
            HealthStore.ResumeWorkoutSession (WorkoutSession);
        }

        public void ReachedNextMile ()
        {
            // Create and save marker event
            var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
            WorkoutEvents.Add (markerEvent);

            // Notify user
            NotifyUserOfReachedMileGoal (++MilesRun);
        }

        public void EndOutdoorRun ()
        {
            // End the current workout session
            HealthStore.EndWorkoutSession (WorkoutSession);
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                StopObservingHealthKitSamples ();
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                if (fromState == HKWorkoutSessionState.Paused) {
                    ResumeObservingHealthkitSamples ();
                } else {
                    ObserveHealthKitSamples ();
                }
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                StopObservingHealthKitSamples ();
                SaveWorkoutSession ();
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);

            // Save HealthKit generated event
            WorkoutEvents.Add (@event);

            // Take action based on the type of event
            switch (@event.Type) {
            case HKWorkoutEventType.Lap:
                ...
                break;
            case HKWorkoutEventType.Marker:
                ...
                break;
            case HKWorkoutEventType.MotionPaused:
                ...
                break;
            case HKWorkoutEventType.MotionResumed:
                ...
                break;
            case HKWorkoutEventType.Pause:
                ...
                break;
            case HKWorkoutEventType.Resume:
                ...
                break;
            }
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();
        public delegate void OutdoorRunMileGoalDelegate (float miles);

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }

        public event OutdoorRunMileGoalDelegate ReachedMileGoal;
        internal void RaiseReachedMileGoal (float miles)
        {
            if (this.ReachedMileGoal != null) this.ReachedMileGoal (miles);
        }

        public event OutdoorRunEventDelegate HealthKitSamplesUpdated;
        internal void RaiseHealthKitSamplesUpdated ()
        {
            if (this.HealthKitSamplesUpdated != null) this.HealthKitSamplesUpdated ();
        }
        #endregion
    }
}
```

## <a name="best-practices"></a>Рекомендации

Apple предлагает использовать следующие рекомендации при разработке и реализации приложений для организации тренировок в iOS 10 и watchOS 3:

- Убедитесь, что приложение watchOS 3 тренировок работает даже в том случае, если она не может подключаться к iPhone и версию приложения для iOS 10.
- Используйте HealthKit расстояние, когда GPS недоступна, так как это может создать примеров расстояний без GPS.
- Разрешить пользователю начать тренировок с Apple Watch и iPhone.
- Чтобы приложение могло отобразить тренировок из других источников (например, другие сторонние приложения) в своих представлений исторических данных.
- Убедитесь, что приложение не не удалены отображения тренировок в данных с предысторией.

## <a name="summary"></a>Сводка

Статья описывает усовершенствования Apple сделала для тренировок приложений в watchOS 3, а также способы их реализации в Xamarin.



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Введение в HealthKit](~/ios/platform/healthkit.md)
