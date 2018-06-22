---
title: Обмен сообщениями Google
description: Этот раздел содержит руководства, описывающие способы реализации Xamarin.Android приложений, с помощью служб обмена сообщениями Google.
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: cf1eaec3dfee7c3457a4614147c9b5564843b2a7
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044669"
---
# <a name="google-messaging"></a>Обмен сообщениями Google

_Этот раздел содержит руководства, описывающие способы реализации Xamarin.Android приложений, с помощью служб обмена сообщениями Google._

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Обмен сообщениями Firebase Cloud](firebase-cloud-messaging.md)

Обмен сообщениями firebase облака (FCM) — это служба, облегчает обмен сообщениями между мобильных приложений и серверных приложений. FCM является преемником Google Google Cloud Messaging. В этой статье содержатся общие сведения о том, как работает FCM, и предоставляет пошаговые процедуры для получения учетных данных, чтобы приложения могли использовать FCM служб.

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[Удаленный уведомления с Firebase облако обмена сообщениями](remote-notifications-with-fcm.md)

Это пошаговое руководство содержит пошаговое объяснение того, как реализуется с помощью Firebase Cloud Messaging удаленного уведомлений (также называемые push-уведомлений) в приложении Xamarin.Android. Описание для реализации различных классах, которые необходимы для обмена данными с сообщениями Firebase облака (FCM), предоставляет примеры того, как настроить Android манифеста для доступа к FCM и демонстрирует подчиненных обмена сообщениями с использованием Firebase Консоль.

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google Cloud Messaging](google-cloud-messaging.md)

В этом разделе представлен общий обзор как Google Cloud Messaging (GCM) маршрутизирует сообщения между приложением и серверов приложений, и предоставляет пошаговые процедуры для получения учетных данных, чтобы приложения могли использовать службы GCM. (Обратите внимание, что GCM был заменен с FCM).

> [!NOTE]
> GCM был заменен с [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM сервер и клиентские API [являются устаревшими](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) и больше не будут доступны как можно скорее 11 апреля 2019 г.

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Удаленный уведомления с Google Cloud Messaging](remote-notifications-with-gcm.md)

Этот раздел содержит пошаговое объяснение способа реализации удаленного уведомления в Xamarin.Android с помощью Google Cloud Messaging.
Здесь объясняется различные компоненты, которые должны использоваться для включения в приложение Google Cloud Messaging.


