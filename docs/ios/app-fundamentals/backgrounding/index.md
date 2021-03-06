---
title: Фоновый режим в Xamarin.iOS
description: Фоновой обработки или фоновой обработки — это процесс, позволяющий приложениям выполнять задачи в фоновом режиме, пока другое приложение выполняется на переднем плане. Данное руководство служит Общие сведения о фоновой обработки в iOS.
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2018
ms.openlocfilehash: a4f5112b6e77ab6e00453c19c766d1e905df1144
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122781"
---
# <a name="backgrounding-in-xamarinios"></a>Фоновый режим в Xamarin.iOS

_Фоновой обработки или фоновой обработки — это процесс, позволяющий приложениям выполнять задачи в фоновом режиме, пока другое приложение выполняется на переднем плане. Данное руководство служит Общие сведения о фоновой обработки в iOS._

Фоновая обработка в мобильных приложениях фундаментально отличается от традиционных концепцию многозадачность на рабочем столе. Настольных компьютеров имеют ряд ресурсов, доступных для приложения, включая площадь экрана, эффективность и памяти. Приложения, возможность запуска side-by-side и сохранять производительных и готовы к использованию. На мобильном устройстве ресурсы гораздо более ограничены. Трудно Показать более чем одним приложением на небольшом экране и работающему с несколькими приложениями на полной скорости бы разряжают батарею. Фоновая обработка является постоянным компромисс между давая приложениям ресурсы для выполнения фоновых задач, которые необходимы для выполнения хорошо и сохранение foregrounded приложения и устройства, быстро реагирующих. IOS и Android включают правила для фоновой обработки, но они обрабатывают самыми разными способами.

В iOS Фоновая обработка распознается как состояние приложения и приложения перемещаются в и из него фона состояние в зависимости от поведения приложения и пользователем. iOS также предлагает несколько вариантов для подключения приложения в фоновом режиме, включая запрос операционной системы для времени для выполнения важных задач, работе в качестве типа известных фона необходимые приложения, а обновление содержимого приложения в интервалы.

В этом руководстве и сопутствующие примеры мы собираемся Узнайте, как выполнять задачи приложения в фоновом режиме. Мы рассматриваются основные понятия и рекомендации и затем проходить через создание реальных приложениях, которые получают обновления расположения в фоновом режиме.

## <a name="contents"></a>Описание

1.  [Общие сведения о фоновой обработке в iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1.  [Демонстрация жизненного цикла приложения](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1.  [Методы фоновой обработки в iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1.  [Пошаговые руководства. Фоновый режим в iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1.  [Руководство по фоновой обработке в iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>Сводка

В этом руководстве мы представили различные способы выполнения фоновой обработки в iOS. Мы в рамках iOS состояния приложений и исследуется роль фоновой обработки играет в жизненном цикле приложения iOS. Кроме того мы узнали, как нам удалось зарегистрировать отдельных задач или всего приложений для работы в фоновом режиме в iOS. Наконец мы подтверждалась пользователями наше объяснение фоновой обработки в iOS путем создания приложений, которые выполняют обновлений в фоновом режиме.



## <a name="related-links"></a>Связанные ссылки

- [Фоновая обработка в Android](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (пример)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
- [Расположение (пример)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Простой фоновой службы передачи (пример)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
- [iOS фонового выполнения](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
