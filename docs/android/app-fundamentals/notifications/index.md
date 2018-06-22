---
title: Уведомления в Xamarin.Android
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: d321c3eb3fe1c882ef9cea6ed1aace3aa1dd953e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762367"
---
# <a name="notifications-in-xamarinandroid"></a>Уведомления в Xamarin.Android


## <a name="overview"></a>Обзор

В этом разделе объясняется, как для реализации уведомлений в Xamarin.Android. Здесь описаны элементы пользовательского интерфейса Android уведомление о и описываются API связанные с созданием и отображает соответствующее уведомление.


## <a name="sections"></a>Разделы

### <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Локальный уведомления в Android](local-notifications.md)

В этом разделе объясняется, как реализовать локальный уведомления в Xamarin.Android. Здесь описаны элементы пользовательского интерфейса Android уведомление о и рассматриваются API-Интерфейс связанные с созданием и отображает соответствующее уведомление. 

### <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[Пошаговое руководство. Использование локальной уведомлений в Xamarin.Android](local-notifications-walkthrough.md)  
 
В этом пошаговом руководстве описывается использование локального уведомления в приложении Xamarin.Android. Он демонстрирует основные принципы создания и публикации уведомления. При щелчке уведомление в ящике уведомления о запуске второго действия. 


## <a name="for-further-reading"></a>Дополнительные сведения

[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase облака обмена сообщениями (FCM) — это служба, облегчает обмен сообщениями между мобильных приложений и серверных приложений. Firebase Cloud Messaging используется для реализации удаленного уведомлений (также называемые push-уведомлений) в приложениях Xamarin.Android.

[Уведомления о](http://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; раздел эта разработчиков под Android является полным руководством для уведомлений Android. Он включает конструктора особенности раздел, который помогает разрабатывать уведомления так, чтобы они соответствовали требованиям Android пользовательского интерфейса. Он предоставляет дополнительные сведения о навигации preserviing при запуске действия, а также описание способа отображения хода выполнения в уведомлений и управления воспроизведение мультимедиа на экране блокировки. 

[NotificationListenerService](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) &ndash; Android эта служба позволяет приложению прослушивать (и взаимодействовать с) учтены все уведомления на устройстве Android, а не только уведомления, которые ваше приложение будет зарегистрировано для получения. Обратите внимание, что пользователю необходимо явно предоставить разрешения для приложения для того, чтобы иметь возможность прослушивать уведомления на устройстве.





## <a name="related-links"></a>Связанные ссылки

- [Локальный уведомления (пример)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Удаленный уведомления (пример)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications/)
