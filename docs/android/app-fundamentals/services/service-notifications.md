---
title: Уведомления службы
description: В этом руководстве рассматривается, как службу Android могут использовать локальные уведомления для отправки сведений для пользователя.
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d56f67254a9eae334fa8ac3f08d3ef270800c309
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103254"
---
# <a name="service-notifications"></a>Уведомления службы

_В этом руководстве рассматривается, как службу Android могут использовать локальные уведомления для отправки сведений для пользователя._


## <a name="service-notifications-overview"></a>Общие сведения о службе уведомлений

Уведомления службы позволяют приложения для отображения сведений для пользователя, даже если приложение Android не находится на переднем плане. Это возможно для уведомлений для выполнения действий для пользователя, например при отображении действия из приложения. В следующем образце кода показано, как служба может отправлять уведомления пользователю:

```csharp
[Service]
public class MyService: Service 
{
    // A notification requires an id that is unique to the application.
    const int NOTIFICATION_ID = 9000;
    
    public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
    {
        // Code omitted for clarity - here is where the service would do something.
    
        // Work has finished, now dispatch anotification to let the user know.
        Notification.Builder notificationBuilder = new Notification.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_notification_small_icon)
            .SetContentTitle(Resources.GetString(Resource.String.notification_content_title))
            .SetContentText(Resources.GetString(Resource.String.notification_content_text));
        
        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.Notify(NOTIFICATION_ID, notificationBuilder.Build());
    }
}
```

На этом снимке экрана приведен пример уведомления, которое отображается:

[![Значок, отображаемый в строке состояния](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

Когда слайды пользователя вниз экрана уведомлений сверху, отображается полный текст уведомления:

![Notication, отображаются в области уведомлений](service-notifications-images/02-fullnotification.png)


## <a name="updating-a-notification"></a>Обновление уведомление

Чтобы обновить уведомление, служба будет повторно опубликовать уведомление, используя тот же идентификатор уведомления. Android, чтобы отобразить или обновить уведомление в строке состояния при необходимости.

```csharp 
void UpdateNotification(string content)
{
   var notification = GetNotification(content, pendingIntent);

   NotificationManager notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
   notificationManager.Notify(NOTIFICATION_ID, notification);
}

Notification GetNotification(string content, PendingIntent intent)
{
   return new Notification.Builder(this)
           .SetContentTitle(tag)
           .SetContentText(content)
           .SetSmallIcon(Resource.Drawable.NotifyLg)
           .SetLargeIcon(BitmapFactory.DecodeResource(Resources, Resource.Drawable.Icon))
           .SetContentIntent(intent).Build();
}
```

Дополнительные сведения об уведомлениях о [локальных уведомлений](~/android/app-fundamentals/notifications/local-notifications.md) раздел [уведомлений Android](~/android/app-fundamentals/notifications/index.md) руководства.


## <a name="related-links"></a>Связанные ссылки

- [Локальные уведомления в Android](~/android/app-fundamentals/notifications/local-notifications.md)
