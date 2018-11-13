---
title: watchOS фоновых задач в Xamarin
description: В этом документе описывается использование фоновых задач в watchOS в Xamarin, рассмотрим типы фоновых задач, с использованием ресурсов, реализация фоновых задач, планирование, рекомендаций и многое другое.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/13/2017
ms.openlocfilehash: 45886d787ecc40c9e11ce0c713ffa22819e29db2
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528823"
---
# <a name="watchos-background-tasks-in-xamarin"></a>watchOS фоновых задач в Xamarin

В watchOS 3 существуют три основных способа, что приложение просмотра можно получить сведения о его: 

- С помощью одного из нескольких новых фоновых задач. 
- Наличие одной из его сложности на циферблате (предоставляя дополнительное время для обновления). 
- Необходимости закрепления новый ПИН-код пользователя к приложению (где его хранится в памяти и часто обновляемых). 

## <a name="keeping-an-app-up-to-date"></a>Обновление приложения

Прежде чем обсуждать все способы, которые разработчик может хранить данные и пользовательский интерфейс приложения watchOS, текущей и обновленной, в этом разделе будет взглянем на типичный набор шаблоны использования и как пользователь может перемещать между их iPhone и их Apple Watch, на протяжении всего дня, на основе  время суток и действия они в настоящее время выполняют (например, обеспечивая тем самым).

Рассмотрим следующий пример:

[![](background-tasks-images/update00.png "Как пользователь может перемещать между их iPhone и их Apple Watch, на протяжении всего дня")](background-tasks-images/update00.png#lightbox)

1. Утром, во время ожидания в строке для кофе пользователь просматривает текущих новостей на их iPhone на несколько минут.
2. Перед выходом из кафе, они быстро проверять погоды с усложнения на их циферблате.
3. До обеда они используют приложение карты на iPhone найти ближайший ресторан и книга резервирования в соответствии с клиентом.
4. Во время поездок в ресторан, они получают уведомления на их Apple Watch и быстро увидеть, они знают, что их обеда встреч запущена позднее.
5. Вечером, они используют приложение карты на iPhone для проверки трафика перед поездкой домой.
6. На пути домой, они получают уведомление об iMessage на их Apple Watch, задавая им овладеть некоторые Молоко и они используют функцию быстрого ответа для отправки ответа «OK».

Из-за «быстрый обзор» (менее трех секунд) характер как пользователь хочет использовать приложение Apple Watch, там обычно не хватает времени для приложения, чтобы получить нужные сведения и обновление его пользовательского интерфейса перед его отображением для пользователя.

С помощью новых API-интерфейсы Apple включила в watchOS 3, приложение можно запланировать для _фоновое обновление_ и готовы требуемой информации перед его запрашивает пользователь. Рассмотрим в качестве примера усложнения погоды, описанных выше:

[![](background-tasks-images/update01.png "Пример усложнения погоды")](background-tasks-images/update01.png#lightbox)

1. Приложение расписания, чтобы быть выведены из спящего режима в системе в определенное время. 
2. Приложение получает сведения, что потребуется для создания обновления.
3. Приложение будет повторно создан пользовательский интерфейс для отображения новых данных.
4. Когда пользователь glances на сложность приложения, он имеет актуальную информацию без необходимости дождитесь обновления.

Как показано выше, watchOS системы в рабочее состояние приложения с помощью одной или нескольких задач, в которых он имеет очень ограниченный пул доступных:

[![](background-tasks-images/update02.png "Приложения с помощью одной или нескольких задач в рабочее состояние системы watchOS")](background-tasks-images/update02.png#lightbox)

Apple предлагает максимально этой задачи (поскольку такие ограниченным ресурсом в приложение), удерживающих до завершения процесса обновления самого приложения.

Система доставляет эти задачи путем вызова нового `HandleBackgroundTasks` метод `WKExtensionDelegate` делегировать. Пример:

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeyWatch.MonkeySeeExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Constructors
        public ExtensionDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request here
            ...
        }
        #endregion
    }
}
```

Когда приложение закончит данной задачи, он возвращает его в систему, помечая его завершения:

[![](background-tasks-images/update03.png "Задача возвращает в систему, помечая его завершения")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Новый фоновых задач

watchOS 3 предоставляет несколько фоновых задач, которые приложение может использовать обновления данных пользователь должен, прежде чем открыть приложение, таких как предоставление содержимого:

- **Фоновые обновления приложения** - [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) задач позволяет приложению обновлять свое состояние в фоновом режиме. Обычно это будет включать другой задачи, такие как загрузка нового содержимого из Интернета с помощью [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Фоновые обновления моментального снимка** - [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) задач позволяет приложению обновлять и его содержимое, так и для пользовательского интерфейса, прежде чем система делает снимок, который будет использоваться для заполнения панели закрепления.
- **Фон Контрольные значения подключения** - [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) задача запускается для приложения при получении данных фона из парном iPhone.
- **Фон сеанса URL-адрес** - [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) задача запускается для приложения, когда фоновая передача требует авторизации или завершения (успешно или ошибка).

В следующих разделах подробно рассматриваются эти задачи.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

`WKApplicationRefreshBackgroundTask` Универсального задача, которая может быть запланировано у вас есть приложение, активирован в будущем:

[![](background-tasks-images/update04.png "WKApplicationRefreshBackgroundTask, активирован в будущем")](background-tasks-images/update04.png#lightbox)

В среде выполнения задачи, приложение может писать никакого локальной обработки, такие как update временной шкалы усложнения или делать выборку некоторых необходимых данных с помощью `NSUrlSession`.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

Система отправит `WKURLSessionRefreshBackgroundTask` когда данных будет завершено, загрузка и готовых к обработке в приложении:

[![](background-tasks-images/update05.png "WKURLSessionRefreshBackgroundTask после завершения загрузки данных")](background-tasks-images/update05.png#lightbox)

Приложение не продолжит выполняться при загрузке данных в фоновом режиме. Вместо этого приложение планирует запрос для данных, а затем он приостановлен, и загрузку данных, только reawakening приложения после завершения загрузки обработки системой.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

В watchOS 3 Apple добавлено закрепления, где пользователи могут присоединять их часто используемыми приложениями и быстрого доступа к ним. Когда пользователь нажимает кнопку на стороне на Apple Watch, будет отображаться коллекции закрепленное приложение моментальных снимков. Выдвигается влево или вправо, чтобы найти нужное приложение, а затем выберите приложение, чтобы запустить его замена моментального снимка с интерфейсом запущенном приложении.

[![](background-tasks-images/update06.png "Замена моментального снимка с помощью интерфейса выполняющегося приложения")](background-tasks-images/update06.png#lightbox)

Система периодически принимает моментальные снимки в пользовательском Интерфейсе приложения (путем отправки `WKSnapshotRefreshBackgroundTask`) и использует эти моментальные снимки для заполнения панели закрепления. watchOS предоставляет приложению возможность обновить его содержимое и пользовательского интерфейса, прежде чем этого моментального снимка.

Моментальные снимки очень важны в watchOS 3, так как они работают как изображения предварительного просмотра и запуска приложения. Если в приложении на панели Dock сопоставляет пользователя, он будет развернуть в полноэкранный режим, введите переднего плана и начать работу с, поэтому крайне важно, что моментальный снимок обновляться:

[![](background-tasks-images/update07.png "Если в приложении на панели Dock сопоставляет пользователя, он будет развернут во весь экран")](background-tasks-images/update07.png#lightbox)

Опять же, система выдаст `WKSnapshotRefreshBackgroundTask` таким образом, чтобы приложение можно подготовить (путем обновления данных и пользовательский Интерфейс) до выполнения моментального снимка:

[![](background-tasks-images/update08.png "Приложение можно подготовить, обновив данные и пользовательский Интерфейс, прежде чем моментального снимка")](background-tasks-images/update08.png#lightbox)

Когда приложение отмечает `WKSnapshotRefreshBackgroundTask` завершена, система будет автоматически создать моментальный снимок пользовательском Интерфейсе приложения.

> [!IMPORTANT]
> Очень важно всегда запланировать ` WKSnapshotRefreshBackgroundTask` после получил новые данные и обновить его пользовательский интерфейс приложения, или пользователь не увидит измененные сведения.




Кроме того когда пользователь получает уведомление от приложения и касается его, чтобы включить его на передний план, на моментальном снимке должен обновляться, так как он действует как на экране запуска:

[![](background-tasks-images/update09.png "Пользователь получает уведомление от приложения и касается его, чтобы включить его на передний план")](background-tasks-images/update09.png#lightbox)

Если прошло более одного часа, так как он взаимодействовал с помощью приложения для watchOS, он будет возможность вернуться к состоянию по умолчанию. Состояние по умолчанию могут иметь разное к различным приложениям и, исходя из структуры приложения, он может вообще не иметь состояние по умолчанию.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

В watchOS 3, Apple встраивается watch подключения с помощью API обновления фона через новый `WKWatchConnectivityRefreshBackgroundTask`. С помощью этой новой функции, приложения iPhone могла предоставлять новые данные аналога приложения watch, пока приложение watchOS выполняется в фоновом режиме:

[![](background-tasks-images/update10.png "Во время работы в фоновом режиме приложение watchOS приложения iPhone могла предоставлять его аналог приложения watch, новые данные")](background-tasks-images/update10.png#lightbox)

Инициирует усложнения Push-уведомлений, контекст приложения, отправив файл или обновление информации о пользователе из приложения iPhone пробуждения приложение Apple Watch в фоновом режиме.

При пробуждении приложении для Apple watch с помощью `WKWatchConnectivityRefreshBackgroundTask` его нужно будет использовать стандартные методы API для получения данных из приложения iPhone.

[![](background-tasks-images/update11.png "Поток данных WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Убедитесь, что сеанс был активирован.
2. Отслеживать новые `HasContentPending` , пока это значение свойства `true`, приложение по-прежнему содержит данные для обработки. Как ранее, приложения должны удерживать задачи до завершения обработки всех данных.
3. Если имеется больше нет данных для обработки (`HasContentPending = false`), пометить задачу завершения, чтобы вернуть его в систему. Если сделать это будет исчерпать все отведенное фона среды выполнения приложения, в результате чего отчет о сбоях.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>Жизненным циклом API фона

Совместное размещение все компоненты, зона API фоновой задачи, это обычно набор действий будет выглядеть следующим образом:

[![](background-tasks-images/update12.png "Жизненным циклом API фона")](background-tasks-images/update12.png#lightbox)

1. Во-первых приложение watchOS планирует фоновой задачи, чтобы быть вернулся как какой-то момент к активности в будущем.
2. Приложение будет активирован системой и отправки задачи.
3. Приложение обрабатывает завершение требовалось любые рабочие задачи.
4. В результате обработки задачи, приложение может потребоваться запланировать дополнительные фоновой задачи, выполняемые больше работы в будущем, таких как скачивание содержимого с помощью `NSUrlSession`.
5. Приложение отмечает завершения задачи и возвращает его в систему.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>Ответственное использование ресурсов

Крайне важно, что приложение watchOS ведет себя ответственно в пределах этой экосистемы, ограничивая его тратой общих ресурсов системы.

Рассмотрим следующий сценарий:

[![](background-tasks-images/update13.png "Приложение watchOS ограничивает его тратой общих ресурсов системы")](background-tasks-images/update13.png#lightbox)

1. Пользователь запускает приложение watchOS в 13:00.
2. Оно планирует задачу для пробуждения и загрузить новое содержимое в течение часа в 14:00.
3. В 13:50 пользователь повторно открывает приложение, которое позволяет обновить данные и пользовательского интерфейса в данный момент.
4. Вместо wake задачи приложение еще раз в 10 минут, приложение следует заново Запланируйте задачу для запуска в час позже в 14:50.

Несмотря на то разных каждое приложение, Apple предлагает выявить закономерности использования, такие как пакеты, представленные выше, для экономии ресурсов системы.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Реализация фоновых задач

Для примера в этом документе будет использоваться фиктивное приложение спортивных MonkeySoccer, которое сообщает пользователю футбольной оценок. 

Рассмотрим следующий сценарий типичное применение:

[![](background-tasks-images/update14.png "Типичная ситуация")](background-tasks-images/update14.png#lightbox)

Избранные футбольной команды пользователя воспроизводится больших совпадение с 7:00 до 9:00, поэтому приложения следует ожидать, что он регулярно проверяет оценку, а также определяет, на 30-минутный интервал обновления.

1. Пользователь открывает приложение и он планирует задачу для фоновое обновление через 30 минут. Интерфейс API фона позволяет фоновой задачи выполняются в определенный момент времени только один тип.
2. Приложение получает задачу и обновляет данные и пользовательского интерфейса, а затем расписания для другого фоновых задач 30 минут. Очень важно, что разработчик помнит о запланировать другой фоновой задачи, или приложение будет никогда не будет повторно пробудится Чтобы получить дополнительные обновления.
3. Опять же приложение получает задачи и обновляет данные, обновляет пользовательский Интерфейс и планирует другой цвет фона задач 30 минут.
4. Тот же процесс повторяется снова.
5. Последняя задача получила фоновые и приложение снова обновляет данные и пользовательского интерфейса. Так как это Окончательная оценка его не расписания для новых обновлений в фоновом режиме. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Планирование для фоновое обновление

Учитывая приведенного выше сценария, MonkeySoccer приложение может использовать следующий код, чтобы запланировать для фоновое обновление:

```csharp
private void ScheduleNextBackgroundUpdate ()
{
    // Create a fire date 30 minutes into the future
    var fireDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

    // Create 
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("LastActiveDate"), NSDate.FromTimeIntervalSinceNow(0));
    userInfo.Add (new NSString ("Reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleBackgroundRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

Он создает новую `NSDate` 30 минут в будущем when приложение хочет быть из спящего режима и создает `NSMutableDictionary` для хранения подробностей поставленную задачу. `ScheduleBackgroundRefresh` Метод `SharedExtension` используется для запроса, планировать задачи.

Система вернет `NSError` Если не удалось запланировать поставленную задачу.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Обработка обновления

Теперь рассмотрим более подробно в 5-минутное окно, показывающая шаги, необходимые для обновления оценка:

[![](background-tasks-images/update15.png "5-минутное окно, показывающая шаги, необходимые для обновления оценка")](background-tasks-images/update15.png#lightbox)

1. В 19:30:02 по активировала системой и учитывая фонового обновления задач приложения. Его приоритетной является получение последних оценок с сервера. См. в разделе [планирования NSUrlSession](#Scheduling-a-NSUrlSession) ниже.
2. В 7:30:05 приложение исходную задачу, система помещает его в спящий режим и продолжат скачиваться в фоновом режиме запрошенные данные.
3. Когда система завершает загрузку, он создает новую задачу для пробуждения приложение, чтобы он мог обрабатывать загруженные сведения. См. в разделе [обработки фоновых задач](#Handling-Background-Tasks) и [обработка загрузки завершение работы](#Handling-the-Download-Completing) ниже. 
4. Приложение сохраняет обновленные сведения и помечает завершения задачи. Разработчик может возникнуть желание обновление пользовательского интерфейса приложения в данный момент, однако Apple предлагает планирования задачи создания моментального снимка для обработки этого процесса. См. в разделе [расписание обновления моментальных снимков](#Scheduling-a-Snapshot-Update) ниже.
5. Приложение получает задачи создания моментального снимка, обновляет его пользовательский интерфейс и помечает завершения задачи. См. в разделе [обработка обновления моментального снимка](#Handling-a-Snapshot-Update) ниже.

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>Планирование NSUrlSession

Чтобы запланировать загрузку последней оценки можно использовать следующий код:

```csharp
private void ScheduleURLUpdateSession ()
{
    // Create new configuration
    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.example.urlsession");

    // Create new session
    var backgroundSession = NSUrlSession.FromConfiguration (configuration);

    // Create and start download task
    var downloadTask = backgroundSession.CreateDownloadTask (new NSUrl ("https://example.com/gamexxx/currentScores.json"));
    downloadTask.Resume ();
}
```

Он настраивает и создает новую `NSUrlSession`, затем использует этот сеанс для создания скачиваемого файла задач с помощью `CreateDownloadTask` метод. Он вызывает `Resume` метод загрузки задач для запуска сеанса.

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>Обработка фоновых задач

Путем переопределения `HandleBackgroundTasks` метод `WKExtensionDelegate`, приложение могло обработать входящие фоновых задач:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
        #endregion

        ...
        
        #region Public Methods
        public void CompleteTask (WKRefreshBackgroundTask task)
        {
            // Mark the task completed and remove from the collection
            task.SetTaskCompleted ();
            PendingTasks.Remove (task);
        }
        #endregion 

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request
            foreach (WKRefreshBackgroundTask task in backgroundTasks) {
                // Is this a background session task?
                var urlTask = task as WKUrlSessionRefreshBackgroundTask;
                if (urlTask != null) {
                    // Create new configuration
                    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

                    // Create new session
                    var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

                    // Keep track of all pending tasks
                    PendingTasks.Add (task);
                } else {
                    // Ensure that all tasks are completed
                    task.SetTaskCompleted ();
                }
            }
        }
        #endregion
        
        ...
    }
}
```

`HandleBackgroundTasks` Метод выполняет циклический переход по все задачи, система отправленных приложения (в `backgroundTasks`) поиск `WKUrlSessionRefreshBackgroundTask`. Если он найден, он подключается к сеансу и присоединяет `NSUrlSessionDownloadDelegate` для обработки загрузки, завершение работы (см. в разделе [обработка завершения загрузки](#Handling-the-Download-Completing) ниже):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Сохраняет дескриптор задачи завершения, добавив его в коллекцию:

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Все задачи, отправленных в приложение требуется для выполнения для любой задачи, которые в настоящее время не обработанные, пометить ее как завершенную:

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>Обработка загрузки завершение работы

Приложение MonkeySoccer использует следующие `NSUrlSessionDownloadDelegate` делегат для обработки загрузки завершение работы и обработать запрошенные данные:

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class BackgroundSessionDelegate : NSUrlSessionDownloadDelegate
    {
        #region Computed Properties
        public ExtensionDelegate WatchExtensionDelegate { get; set; }

        public WKRefreshBackgroundTask Task { get; set; }
        #endregion

        #region Constructors
        public BackgroundSessionDelegate (ExtensionDelegate extensionDelegate, WKRefreshBackgroundTask task)
        {
            // Initialize
            this.WatchExtensionDelegate = extensionDelegate;
            this.Task = task;
        }
        #endregion

        #region Override Methods
        public override void DidFinishDownloading (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, NSUrl location)
        {
            // Handle the downloaded data
            ...

            // Mark the task completed
            WatchExtensionDelegate.CompleteTask (Task);

        }
        #endregion
    }
}
```

При инициализации, он сохраняет дескриптор к обоим `ExtensionDelegate` и `WKRefreshBackgroundTask` , порожденных его. Этот параметр переопределяет `DidFinishDownloading` метод для обработки загрузки завершение работы. Затем использует `CompleteTask` метод `ExtensionDelegate` для уведомления задачи, ее завершения и удалите его из коллекции ожидающих задач. См. в разделе [обработки фоновых задач](#Handling-Background-Tasks) выше.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Планирование обновления моментального снимка

Для планирования задачи создания моментального снимка для обновления пользовательского интерфейса с последней оценки можно использовать следующий код:

```csharp
private void ScheduleSnapshotUpdate ()
{
    // Create a fire date of now
    var fireDate = NSDate.FromTimeIntervalSinceNow (0);

    // Create user info dictionary
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
    userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleSnapshotRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

Так же, как `ScheduleURLUpdateSession` выше метод, он создает новый `NSDate` приложение хочет быть из спящего режима и создает `NSMutableDictionary` для хранения подробностей поставленную задачу. `ScheduleSnapshotRefresh` Метод `SharedExtension` используется для запроса, планировать задачи.

Система вернет `NSError` Если не удалось запланировать поставленную задачу.

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>Обработка обновления моментального снимка

Для обработки задачи создания моментального снимка, `HandleBackgroundTasks` метод (см. в разделе [обработки фоновых задач](#Handling-Background-Tasks) выше) изменяется, чтобы выглядеть следующим образом:

```csharp
public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
{
    // Handle background request
    foreach (WKRefreshBackgroundTask task in backgroundTasks) {
        // Take action based on task type
        if (task is WKUrlSessionRefreshBackgroundTask) {
            var urlTask = task as WKUrlSessionRefreshBackgroundTask;

            // Create new configuration
            var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

            // Create new session
            var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

            // Keep track of all pending tasks
            PendingTasks.Add (task);
        } else if (task is WKSnapshotRefreshBackgroundTask) {
            var snapshotTask = task as WKSnapshotRefreshBackgroundTask;

            // Update UI
            ...

            // Create a expiration date 30 minutes into the future
            var expirationDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

            // Create user info dictionary
            var userInfo = new NSMutableDictionary ();
            userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
            userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

            // Mark task complete
            snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
        } else {
            // Ensure that all tasks are completed
            task.SetTaskCompleted ();
        }
    }
}
```

Метод проверяет тип обработки задачи. Если это `WKSnapshotRefreshBackgroundTask` он получает доступ к задаче:

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

Метод обновляет пользовательский интерфейс, а затем создает `NSDate` указывает системе, если моментальный снимок будут находиться устаревшие. Он создает `NSMutableDictionary` с помощью сведений о пользователе для описания нового моментального снимка и метки моментальных снимков, выполненной с помощью этой информации:

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

Кроме того он также сообщает задачи создания моментального снимка, приложение не возвращает к состоянию по умолчанию (в качестве первого параметра). Приложения, которые не имеют концепции состояние по умолчанию всегда необходимо присвоить этому свойству `true`.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Работает неэффективно

Как показано в приведенном выше примере окна в течение пяти минут, MonkeySoccer приложение для обновления его оценки эффективности работы и используя новый watchOS 3 фоновые задачи, приложение только была активна в течение 15 секунд: 

[![](background-tasks-images/update16.png "Приложение было только активные в течение 15 секунд")](background-tasks-images/update16.png#lightbox)

Это уменьшает влияние, которое приложение будет иметь на доступные ресурсы Apple Watch и батареи, а также позволяет приложению улучшают работу других приложений, выполняющихся на часах.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Как работает планирования

Хотя приложение watchOS 3 находится на переднем плане, она всегда планируется для выполнения и может выполнить любой тип обработки таких как обновление данных или перерисовывает его пользовательского интерфейса. Когда приложение перемещает в фоновом режиме, обычно он приостановлен системой, и все операции среды выполнения приостановлено. 

Пока приложение находится в фоновом режиме, могут являться целевыми системой для быстрого выполнения определенной задачи. Таким образом в watchOS 2, система временно пробуждения в приложении фона для работы, например обработка уведомления уведомления или для обновления приложения усложнения. В watchOS 3 существует несколько новых способов, которые приложение может выполняться в фоновом режиме.

Пока приложение находится в фоновом режиме, система накладывает некоторые ограничения на его:

- Ему только присваивается несколько секунд для выполнения любой задачи. Система учитывает не только количество времени, передан, но также какое количество энергии ЦП приложением для получения этого ограничения.
- Любое приложение, которое превышает предел будет необходимо завершить с помощью следующие коды ошибок:
    - **ЦП** -0xc51bad01
    - **Время** -0xc51bad02
- Система будет накладывают разные ограничения на основе типа он поступает запрос на выполнение фоновой задачи. Например `WKApplicationRefreshBackgroundTask` и `WKURLSessionRefreshBackgroundTask` задач, получают немного больше времени сред выполнения над другими типами фоновых задач.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Сложности и обновления приложений

Помимо новых фоновых задач, которая добавлена в watchOS 3 Apple сложности приложения watchOS может оказывать влияние на том, как и когда приложение получает обновления в фоновом режиме.

Сложности, небольшой визуальные элементы, которые предоставляют полезную информацию с первого взгляда. В зависимости от выбранного циферблате пользователь имеет возможность настройки оформлений с одной или нескольких сложностью, которая может быть предоставленный приложение просмотра в watchOS 3.

Если пользователь включает в себя один из сложностей приложения на их циферблате, он дает приложения обновлены следующие преимущества:

- Система помнить все готово для запуска приложения состояние, когда он пытается запустить приложение в фоновом режиме, остается в памяти и обеспечивает дополнительное время для обновления.
- Сложности гарантируется по меньшей мере 50 обновлений Push-уведомлений в день.

Разработчик всегда должны стремиться создавать привлекательные сложности для своих приложений, чтобы заставить пользователя добавления их к их циферблате по причинам, перечисленных выше.

В watchOS 2 сложности были основным способом, что приложение получено среды выполнения в фоновом режиме. В watchOS 3, в приложении усложнения по-прежнему быть гарантирована для получения нескольких обновлений в час, однако он может использовать `WKExtensions` запрашивать дополнительные среды выполнения для обновления его сложности.

Взгляните на следующий код, используемый для обновления усложнения из приложения iPhone подключенных:

```csharp
using System;
using WatchConnectivity;
using UIKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
...

private void UpdateComplication ()
{

    // Get session and the number of remaining transfers
    var session = WCSession.DefaultSession;
    var transfers = session.RemainingComplicationUserInfoTransfers;

    // Create user info dictionary
    var iconattrs = new Dictionary<NSString, NSObject>
        {
            {new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0)},
            {new NSString ("reason"), new NSString ("UpdateScore")}
        };

    var userInfo = NSDictionary<NSString, NSObject>.FromObjectsAndKeys (iconattrs.Values.ToArray (), iconattrs.Keys.ToArray ());

    // Take action based on the number of transfers left
    if (transfers < 1) {
        // No transfers left, either attempt to send or inform
        // user of situation.
        ...
    } else if (transfers < 11) {
        // Running low on transfers, only send on important updates
        // else conserve for a significant change.
        ...
    } else {
        // Send data
        session.TransferCurrentComplicationUserInfo (userInfo);
    }
}
```

Она использует `RemainingComplicationUserInfoTransfers` свойство `WCSession` чтобы увидеть, сколько с высоким приоритетом передает приложения вышел за день, а затем принимает меры на основе этого номера. Если не хватает на передачу начала приложения, он воздержаться от отправки незначительные изменения и отправлять информацию только при наличии значительных изменений.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>Планирование и панели закрепления

В watchOS 3 Apple добавлено закрепления, где пользователи могут присоединять их часто используемыми приложениями и быстрого доступа к ним. Когда пользователь нажимает кнопку на стороне на Apple Watch, будет отображаться коллекции закрепленное приложение моментальных снимков. Выдвигается влево или вправо, чтобы найти нужное приложение, а затем выберите приложение, чтобы запустить его замена моментального снимка с интерфейсом запущенном приложении.

[![](background-tasks-images/dock01.png "Dock")](background-tasks-images/dock01.png#lightbox)

Система периодически делать снимки пользовательском Интерфейсе приложения и использует эти моментальные снимки для заполнения документы. watchOS предоставляет приложению возможность обновить его содержимое и пользовательского интерфейса, прежде чем этого моментального снимка.

Приложения, которые закреплены на панель закрепления можно ожидать, что следующее:

- Они получат как минимум одно обновление в час. Это включает в себя задачу обновления приложения и задачи создания моментального снимка.
- Бюджет обновление распределяется между всех приложений на панели закрепления. Поэтому меньше приложений, которые пользователь закреплен, повышают вероятность каждого приложения будут получать обновления.
- Приложения будут храниться в памяти, поэтому приложение будет возобновлена быстро при выборе панели закрепления.

Последний пользователь запустил приложение будет считаться _недавно использовавшиеся_ приложения и будет занимать последней ячейкой для закрепления. После этого существует пользователь может выбрать, чтобы окончательно закрепить ее на панель закрепления. Недавно использовавшиеся будет рассматриваться, как и любые другие избранные приложения пользователь уже закреплены на панели закрепления.

> [!IMPORTANT]
> Приложения, которые были добавлены только для начального экрана не получит регулярное расписание. Чтобы получать регулярные планирования и фона обновления, приложения _необходимо_ добавляться на панель закрепления.

Как уже говорилось ранее в этом документе, моментальные снимки являются очень важно для watchOS 3, так как они работают как изображения предварительного просмотра и запуска приложения. Если в приложении на панели Dock сопоставляет пользователя, он будет развернуть в полноэкранный режим, введите переднего плана и начать работу с, поэтому крайне важно, что моментальный снимок обновляться.

Возможны ситуации, когда система решает, что ей новый моментальный снимок пользовательском Интерфейсе приложения. В этом случае запрос моментального снимка не учитываются для бюджета среды выполнения приложения. Ниже будет активировать запрос моментальных снимков системы:

- Это обновление усложнения временной шкалы.
- Взаимодействие пользователя с уведомление от приложения.
- Переключение с переднего плана в фоновом режиме состояние.
- Через час состояния фона поэтому приложение может возвращать до значений по умолчанию.
- Когда watchOS первой загрузке.

<a name="Best-Practices" />

## <a name="best-practices"></a>Рекомендации 

Apple предлагает следующие рекомендации при работе с фоновыми задачами:

- Запланируйте так часто, как приложения должен быть обновлен. Каждый раз при запуске приложения следует повторно оценить будущие потребности в его и изменить при необходимости.
- Если приложение не требует обновления, система отправляет фоновой задачи обновления, отложите до обновления требуется фактически.
- Примите во внимание все возможности среды выполнения, доступных для приложения.
    - Активация закрепления и переднего плана.
    - Уведомления.
    - Усложнение обновления.
    - Фоновое обновление.
- Используйте `ScheduleBackgroundRefresh` для среды выполнения фоновой общего назначения, такие как:
    - Опрос у системы информацию.
    - Планировать будущее `NSURLSessions` фона запрашивать данные. 
    - Переходы известное время.
    - Запуска обновлений усложнения.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Рекомендации по обеспечению моментальных снимков

При работе с обновлений моментальных снимков, Apple делает следующее:

- Сделать недействительным моментальных снимков только в том случае, если требуется, например, когда были произведены существенные изменения содержимого.
- Избегайте высоких частот недействительности моментального снимка. Например приложение таймера не следует обновления моментального снимка каждую секунду, его следует делать только при окончании таймера.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Поток данных приложения

Apple предлагает следующие для работы с потоком данных:

[![](background-tasks-images/update17.png "Схема потока данных приложения")](background-tasks-images/update17.png#lightbox)

Внешнее событие (например, подключение к Watch) в рабочее состояние приложения. Это заставляет приложение для обновления своей модели (который представляет текущее состояние приложения). В результате изменения модели данных приложения будет необходимо обновить его сложности, запросить новый моментальный снимок, возможно запустить фона `NSURLSession` извлечь дополнительные данные и дальнейшей запланировать фоновое обновление.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>Жизненный цикл приложения

Из-за панели закрепления и возможность закрепить часто используемыми приложениями, к нему, что Apple пользователи переходят между гораздо больше приложений гораздо чаще, затем они поддерживались в watchOS 2. Таким образом приложения должны быть готовы обрабатывать это изменение быстрого и перехода между состояниями переднего плана и фона.

Apple имеет следующие рекомендации:

- Убедитесь, что приложение завершения любой фоновой задачи, как можно скорее при входе в активации переднего плана.
- Убедитесь, что всю работу на переднем плане перед переключением в фоновом режиме путем вызова `NSProcessInfo.PerformExpiringActivity`.
- При тестировании приложения в симуляторе watchOS, ни одна из задач бюджеты будут действовать, можно обновить приложения так, чтобы правильно протестировать функцию.
- Всегда проверяйте на реальном оборудовании Apple Watch, чтобы убедиться, что приложение не работает после его бюджеты перед публикацией в iTunes Connect.
- Apple предлагает рановато Apple Watch зарядному устройству во время тестирования и отладки.
- Убедитесь в том, тщательное тестирование как холодный запуск и возобновление работы приложения.
- Убедитесь, что все задачи приложения выполняются в настоящее время.
- Варьировать число приложений, которые закреплены на панели Dock для тестирования лучших и наихудшей сценариев вариантов.

<a name="Summary" />

## <a name="summary"></a>Сводка

Статья описывает усовершенствования, внесенные в Apple для watchOS, и как они могут использоваться для обновления приложение просмотра. Во-первых, она охватывала все новые добавленные Apple фоновой задачи в watchOS 3. Затем рассматривается жизненным циклом API фона и способ реализации фоновых задач в приложении Xamarin watchOS. Наконец рассматривается как планирования works и дал некоторые рекомендации.



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
