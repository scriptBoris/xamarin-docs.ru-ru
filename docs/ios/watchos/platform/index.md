---
title: функции платформы watchOS
description: Этот документ содержит ссылки на различные руководства, описывающие функции платформы watchOS, таких как Apple Pay, уведомления, сложности, упреждающие предложения, тренировки приложений и многое другое.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: e915ec38ed29b6ef2a0710c9dad10cf339c3a3ab
ms.sourcegitcommit: cfb72be633e335147d156af3ef9527151b9e31d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030682"
---
# <a name="watchos-platform-features"></a>функции платформы watchOS

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[Введение в watchOS 5](introduction-to-watchos5/index.md)

> [!WARNING]
> Поддержка Xamarin watchOS 5 в настоящее время в предварительной версии, что означает, что он может содержать ошибки, не все компоненты, и могут измениться.
> Используйте его только для экспериментов.

Этот документ содержит общий обзор новых и обновленных функциях в watchOS 5, которые доступны для использования при создании приложения для watchOS с помощью Xamarin.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Введение в watchOS 4](introduction-to-watchos4.md)

Этот документ содержит обзор функций добавляются и обновляются в watchOS 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Введение в watchOS 3](introduction-to-watchos3/index.md)

В этой статье описываются новые и обновленные интерфейсы API в watchOS 3.

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Улучшения Apple Pay](~/ios/watchos/platform/apple-pay.md)

В watchOS 3 PassKit framework была расширена для поддержки безопасного, в приложении платежи (физических товаров и услуг) для приложений, запущенных в Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Фоновые задачи](~/ios/watchos/platform/background-tasks.md)

watchOS 3 представлены несколько фоновых задач, которые приложение может использовать для обновления информации предоставление содержимого необходимые пользователю перед его открытием.

## <a name="notificationsnotificationsmd"></a>[Уведомления](notifications.md)

Узнайте, как предоставить пользовательские уведомления, обработка в приложение для Apple watch.

## <a name="complicationscomplicationsmd"></a>[Усложнения](complications.md)

Добавление поддержки усложнения для отображения свежих данных на циферблате.

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Упреждающие предложения](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 позволяет приложению заранее представлять сведения для пользователя в пределах заданного контекстов. Для поддержки этой возможности [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) теперь включает `MapItem` свойство, которое позволяет приложению указывать информацию о расположении для дальнейшего использования в других приложениях.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Быстрые методы взаимодействия](~/ios/watchos/platform/quick-interaction-techniques.md)

Обеспечивает взаимодействие с пользователем быстрого необходимы для создания привлекательных приложений Apple Watch и трудности. Новое для watchOS 3, Apple была добавлена поддержка распознавателей жестов, доступ к цифровой корона и новые методы уведомления пользователей и навигации. Это, а также добавлена поддержка SceneKit и SpriteKit, позволяют разработчикам легко создавать подробные и glanceable интерфейсы, которые легко и быстро реагирующих.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Усовершенствования приложения для тренировок](~/ios/watchos/platform/workout-apps.md)

Новое для watchOS 3, тренировки связанные приложения имеют возможность работать в фоновом режиме на Apple Watch. Чтобы включить эту функцию (и получить доступ к данным HealthKit), приложение должно включать `WKBackgroundModes` в `Info.plist` файла со значением `workout-processing`.
