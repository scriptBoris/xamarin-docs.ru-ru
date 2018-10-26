---
title: Методы фоновой обработки в iOS
description: 'Этот документ содержит ссылки на руководства, описывающие различные методы backgrounding в iOS: фоновых задач, фоновой службой передачи, обработку в фоновом режиме и удаленные уведомления.'
ms.prod: xamarin
ms.assetid: 011A8D48-1CDC-486A-A2B0-C4946118E7A9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 00cca0c75cc79858f6edda5d6fb954611d81161b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119752"
---
# <a name="ios-backgrounding-techniques"></a>Методы фоновой обработки в iOS

В следующих разделах мы рассмотрим следующие функции iOS параллельно с существующими параметрами backgrounding:

-  [Фоновые задачи, обусловленная](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background_tasks_in_iOS_7) -сохранить батареи, запуск фоновых задач обусловленная блоками, когда устройство находится в активном состоянии, для остальных действий.
-  [Фоновая служба передачи](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background-transfers) - надежно отправлять и загружать файлы независимо от размера сети состояние или файл.
-  [Фон Fetch](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#background_fetch) -обновление приложения в фоновом режиме с интервалами, определяемых системой.
-  [Удаленные уведомления](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#remote_notifications) -использование Push-уведомлений для активации обновления содержимого в фоновом режиме, прежде чем пользователь запускает приложение, с возможностью уведомления пользователя или автоматическое обновление.
-  **Фоновые обновления пользовательского интерфейса** – подготовка пользовательского интерфейса приложения для пользователя и обновить моментальный снимок приложения, в фоновом режиме.
