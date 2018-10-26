---
title: Технологии нерекомендуемых уведомлений в Xamarin.iOS
description: В этом документе описаны технологии уведомлений iOS, которые признаны нерекомендуемыми платформу уведомления для пользователей в iOS 10.
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2016
ms.openlocfilehash: 7b0c2d99412f7a34c0e9c95a282d5d74dbe8dd78
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114331"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>Технологии нерекомендуемых уведомлений в Xamarin.iOS

В этом разделе показано, как реализовать локальных и Push-уведомлений в Xamarin.iOS. Он будет объясняются различные элементы пользовательского интерфейса iOS уведомления о и рассматриваются API-Интерфейс связанных с созданием и отображение уведомления.

> [!IMPORTANT]
> Сведения в этом разделе, относятся к iOS 9 и предыдущий, она была оставлена здесь для поддержки более старых версий iOS. IOS 10 и более поздние версии, см. в разделе [руководство по Framework уведомление пользователя](~/ios/platform/user-notifications/index.md) за поддержку локальных и удаленных уведомлений на устройства iOS.

## <a name="sections"></a>Разделы

<a name="Local Notifications In iOS" />

##  <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[Локальные уведомления в iOS](local-notifications-in-ios.md)

В этом разделе описывается, как реализовать локальных уведомлений в Xamarin.iOS. Он будет объясняются различные элементы пользовательского интерфейса iOS уведомления о и рассматриваются API-Интерфейс связанных с созданием и отображение уведомления.

<a name="Local Notifications Walkthrough" />

##  <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[Пошаговое руководство. Использование локальных уведомлений в Xamarin.iOS](local-notifications-in-ios-walkthrough.md)

В этом разделе мы рассмотрим способы использования локальных уведомлений в приложении Xamarin.iOS. Его мы рассмотрим основы создания и публикации, появится предупреждение, когда приложение получит уведомление.

<a name="Remote Notifications In iOS" />

##  <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[Удаленные уведомления в iOS](remote-notifications-in-ios.md)

В этом разделе будут рассмотрены Push-уведомлений в iOS. В нем представлены Apple Push-уведомления шлюза Service (APNS) и роль, которую он играет в публикации уведомлений в приложения iOS. Здесь объясняется, как для создания сертификатов безопасности, необходимые для включения Push-уведомлений и обсудить. Наконец в этом разделе рассматриваются некоторые задачи по обслуживанию, серверы приложений необходимо выполнить для отслеживания клиентских мобильных устройств.

## <a name="related-links"></a>Связанные ссылки

- [Уведомления (пример)](https://developer.xamarin.com/samples/monotouch/Notifications/)
