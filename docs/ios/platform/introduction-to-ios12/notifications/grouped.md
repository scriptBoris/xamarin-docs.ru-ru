---
title: Сгруппированные уведомлений в Xamarin.iOS
description: С помощью iOS 12 существует возможность группы уведомлений в центре уведомлений или на экране блокировки с приложения или потока. В этом документе описываются способы отправки потоками и несвязанным уведомлений с помощью Xamarin.iOS.
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: 278986b29e629995a202f474242670f5524c45ff
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111604"
---
# <a name="grouped-notifications-in-xamarinios"></a>Сгруппированные уведомлений в Xamarin.iOS

По умолчанию iOS 12 помещает все уведомления приложения в группе. Блокировка экрана и центр уведомлений отображения в виде стека с последнего уведомления в верхней части этой группы. Пользователей можно развернуть в группу, чтобы все уведомления, она содержит и отклонить группы в целом см. в разделе.

Приложения также могут группы уведомлений потоком, что упрощает для пользователей поиск и взаимодействовать с нужной информации им интересны.

## <a name="sample-app-groupednotifications"></a>Пример приложения: GroupedNotifications

Чтобы узнать, как использовать сгруппированные уведомления с помощью Xamarin.iOS, взгляните на [GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications) примера приложения.

Этот пример приложения имитирует диалоги с различных друзьями, отправки уведомления для каждого сообщения и сгруппировать их потоком. Он также демонстрирует, как несвязанным land уведомления в группе уровня приложения.

Фрагменты кода, в этом руководстве, взяты из этого примера приложения.

## <a name="request-authorization-and-allow-foreground-notifications"></a>Запросить авторизацию и разрешить уведомления переднего плана

Приложения для отправки локального уведомления, оно должно запросить разрешение на это. В примере приложения [ `AppDelegate` ](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/), [ `FinishedLaunching` ](https://developer.xamarin.com/api/member/UIKit.UIApplicationDelegate.FinishedLaunching/p/UIKit.UIApplication/Foundation.NSDictionary/) метод запрашивает это разрешение:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    center.RequestAuthorization(UNAuthorizationOptions.Alert, (bool success, NSError error) =>
    {
        // Set the Delegate regardless of success; users can modify their notification
        // preferences at any time in the Settings app.
        center.Delegate = this;
    });
    return true;
}
```

[ `Delegate` ](https://developer.xamarin.com/api/property/UserNotifications.UNUserNotificationCenter.Delegate/) (Задайте выше) для [ `UNUserNotificationCenter` ](https://developer.xamarin.com/api/type/UserNotifications.UNUserNotificationCenter/) решает ли приложение переднего плана отобразится уведомление о входящих путем вызова обработчик завершения, передаваемый [`WillPresentNotification`](https://developer.xamarin.com/api/member/UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification/p/UserNotifications.IUNUserNotificationCenterDelegate/UserNotifications.UNUserNotificationCenter/UserNotifications.UNNotification/System.Action%7BUserNotifications.UNNotificationPresentationOptions%7D/):

```csharp
[Export("userNotificationCenter:willPresentotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

[ `UNNotificationPresentationOptions.Alert` ](https://developer.xamarin.com/api/type/UserNotifications.UNNotificationPresentationOptions/) Параметр указывает, что приложение должно отображать сообщение, но не воспроизведение звука или обновить эмблему.

## <a name="threaded-notifications"></a>Многопоточные уведомления

Выберите пример приложения **сообщение с Алисой** кнопку несколько раз, чтобы их отправки уведомлений для диалога с именем Анна друзьям.
Так как этот диалог уведомления входят в том же потоке, блокировка экрана и центр уведомлений сгруппировать их вместе.

Чтобы начать диалог с другой другом, коснитесь **выберите запись нового друга** кнопки. Уведомления для этого диалога, появляются в отдельную группу.

### <a name="threadidentifier"></a>ThreadIdentifier

Любое время, в примере приложения запускает новый поток, он создает идентификатор, уникальный поток:

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

Чтобы отправить уведомление потоками, пример приложения:

- Проверяет, имеет ли приложение авторизации для отправки уведомления.
- Создает [`UNMutableNotificationContent`](https://developer.xamarin.com/api/type/UserNotifications.UNMutableNotificationContent/)
объект для уведомления содержимого и устанавливает его [`ThreadIdentifier`](https://developer.xamarin.com/api/property/UserNotifications.UNMutableNotificationContent.ThreadIdentifier/)
Идентификатор потока, созданный ранее.
- Создает запрос и планирует уведомления:

```csharp
async partial void ScheduleThreadedNotification(UIButton sender)
{
    var center = UNUserNotificationCenter.Current;

    UNNotificationSettings settings = await center.GetNotificationSettingsAsync();
    if (settings.AuthorizationStatus != UNAuthorizationStatus.Authorized)
    {
        return;
    }

    string author =  // ...
    string message = // ...

    var content = new UNMutableNotificationContent()
    {
        ThreadIdentifier = threadId,
        Title = author,
        Body = message,
        SummaryArgument = author
    };

    var request = UNNotificationRequest.FromIdentifier(
        Guid.NewGuid().ToString(),
        content,
        UNTimeIntervalNotificationTrigger.CreateTrigger(1, false)
    );

    center.AddNotificationRequest(request, null);

    // ...
}
```

Все уведомления из одного приложения с тем же идентификатором потока будут отображаться в той же группе уведомлений.

> [!NOTE]
> Чтобы задать идентификатор потока на удаленных уведомлений, добавьте `thread-id` ключа уведомления полезные данные JSON. См. в разделе Apple [созданием удаленного уведомления](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) документа для получения дополнительных сведений.

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument` Указывает влияние текст сводки, который отображается в левом нижнем углу группы уведомлений, к которому относится уведомление уведомление. iOS объединяет текст сводки из уведомлений в той же группе для создания в целом сводное описание.

Пример приложения использует автора сообщения в качестве аргумента сводки. При таком подходе текст сводки для группы шесть уведомлений с помощью Алиса может быть **5 Дополнительные уведомления от Алисы и мне**.

## <a name="unthreaded-notifications"></a>Несвязанным уведомления

Каждое нажатие примера приложения **напоминания о встрече** кнопка отправляет один из различных уведомления памятки встречи. Так как эти напоминания не являются структурированными, они отображаются в группе уведомления на уровне приложения на экране блокировки и в центре уведомлений.

Отправка несвязанным уведомления, пример приложения `ScheduleUnthreadedNotification` метод использует аналогичный код, как описано выше.
Тем не менее, она не присваивает `ThreadIdentifier` на `UNMutableNotificationContent` объекта.

## <a name="related-links"></a>Связанные ссылки

- [Пример приложения — GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications)
- [Платформа уведомлений пользователя в Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [Новые возможности в уведомления для пользователей (WWDC 2018 г.)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [С помощью сгруппированные уведомления (WWDC 2018 г.)](https://developer.apple.com/videos/play/wwdc2018/711/)
- [Рекомендации и новые возможности в уведомления для пользователей (WWDC 2017 г.)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Создание удаленного уведомления (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
