---
title: Методы фоновой обработки в iOS
description: 'Этот документ содержит ссылки на руководства, описывающие различные методы backgrounding в iOS: фоновых задач, фоновой службой передачи, обработку в фоновом режиме и удаленные уведомления.'
ms.prod: xamarin
ms.assetid: 011A8D48-1CDC-486A-A2B0-C4946118E7A9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 33c4613e3698556b41a0d01acf2a4ac359ca5dd8
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350973"
---
# <a name="ios-backgrounding-techniques"></a>Методы фоновой обработки в iOS

В следующих разделах мы рассмотрим следующие функции iOS параллельно с существующими параметрами backgrounding:

-  [Фоновые задачи, обусловленная](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background_tasks_in_iOS_7) -сохранить батареи, запуск фоновых задач обусловленная блоками, когда устройство находится в активном состоянии, для остальных действий.
-  [Фоновая служба передачи](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background-transfers) - надежно отправлять и загружать файлы независимо от размера сети состояние или файл.
-  [Фон Fetch](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#background_fetch) -обновление приложения в фоновом режиме с интервалами, определяемых системой.
-  [Удаленные уведомления](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#remote_notifications) -использование Push-уведомлений для активации обновления содержимого в фоновом режиме, прежде чем пользователь запускает приложение, с возможностью уведомления пользователя или автоматическое обновление.
-  **Фоновые обновления пользовательского интерфейса** – подготовка пользовательского интерфейса приложения для пользователя и обновить моментальный снимок приложения, в фоновом режиме.
