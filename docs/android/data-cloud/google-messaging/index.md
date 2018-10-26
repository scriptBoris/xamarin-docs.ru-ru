---
title: Обмен сообщениями Google
description: В этом разделе содержатся руководства, описывающие способы реализации приложений Xamarin.Android с помощью службы обмена сообщениями Google.
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: 8c2e3705f0f867b6993d0bdcb22f6672c853498e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121910"
---
# <a name="google-messaging"></a>Обмен сообщениями Google

_В этом разделе содержатся руководства, описывающие способы реализации приложений Xamarin.Android с помощью службы обмена сообщениями Google._

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Обмен сообщениями Firebase Cloud](firebase-cloud-messaging.md)

Firebase Cloud Messaging (FCM) — это служба, которая облегчает обмен сообщениями между мобильные приложения и серверные приложения. FCM является преемником Google Google Cloud Messaging. В этой статье представлен обзор принципов работы FCM, и предоставляет пошаговые процедуры для получения учетных данных, таким образом, чтобы ваше приложение может использовать службы FCM.

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[Удаленные уведомления с помощью Firebase Cloud Messaging](remote-notifications-with-fcm.md)

Это пошаговое руководство содержит пошаговое объяснение способа использования Firebase Cloud Messaging для реализации удаленных уведомлений (также называемые Push-уведомления) в приложениях Xamarin.Android. Он показывает, как реализовать различные классы, которые необходимы для обмена данными с помощью Firebase Cloud Messaging (FCM), приводятся примеры того, как настроить манифест Android для доступа к FCM и демонстрирует подчиненных обмена сообщениями с помощью Firebase Консоль.

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google Cloud Messaging](google-cloud-messaging.md)

В этом разделе представлен общий обзор как Google Cloud Messaging (GCM) маршрутизирует сообщения между приложением и сервером приложений, а также пошаговая процедура для получения учетных данных, таким образом, чтобы ваше приложение может использовать службы GCM. (Обратите внимание, что GCM была заменена FCM).

> [!NOTE]
> GCM была заменена [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM сервер и клиентские API-интерфейсы [стали нерекомендуемыми](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) и больше не будут доступны как можно скорее 11 апреля 2019 г.

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Удаленные уведомления с помощью Google Cloud Messaging](remote-notifications-with-gcm.md)

Этот раздел содержит пошаговое описание реализации удаленных уведомлений в Xamarin.Android с помощью Google Cloud Messaging.
Здесь описываются различные компоненты, необходимо обеспечить, чтобы включить Google Cloud Messaging в приложении Android.


