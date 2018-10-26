---
title: Данные и облачные службы в приложениях Xamarin.iOS
description: Этот документ содержит ссылки на руководства, описывающие способы работы с локальными данными, iCloud и CloudKit в приложении Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 21a6c1c0c0ceb5596a056f0818dec39041808504
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117155"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>Данные и облачные службы в приложениях Xamarin.iOS

##  <a name="data-accessiosdata-clouddataindexmd"></a>[Доступ к данным](~/ios/data-cloud/data/index.md)

Большинство приложений имеют некоторые требования, чтобы сохранить данные на устройстве локально. Если объем данных, не является тривиально небольшой, обычно для этого базу данных и уровень данных в приложении для управления доступом к базе данных. iOS имеет ядро базы данных Sqlite, «заложены», и доступ к данным стало проще благодаря платформе Xamarin, который поставляется вместе с поставщиком данных SQLite.

##  <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

Хранилище iCloud API в iOS 5 позволяет приложениям сохранять пользовательские документы и данные приложения в центральном расположении и доступ к ним получить все его устройства.

##  <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

Инфраструктура CloudKit упрощает разработку приложений, доступа к iCloud. Сюда входят методы для получения данных приложений и средств права, а также возможность обеспечить безопасное хранение сведений о приложении. Этот комплект предоставляет пользователям уровня анонимности, разрешив доступ к приложениям с их iCloud идентификаторы без обмена личными данными.