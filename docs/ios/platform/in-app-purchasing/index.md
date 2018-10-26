---
title: Приобретение в Xamarin.iOS в приложении
description: В этом документе описывается, как продавать цифровых продуктов и служб с помощью API-интерфейсы StoreKit. Он содержит ссылки на руководства, посвященные конфигурации, готовых к использованию продуктов, неготовых продуктов, транзакции, подписок и многое другое.
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4b301c18ea0e69c818cf65b3b7df1cc8351350f5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119336"
---
# <a name="in-app-purchasing-in-xamarinios"></a>Приобретение в Xamarin.iOS в приложении

приложения iOS можно продавать цифровых продуктов или служб с помощью StoreKit — это набор API, предоставляемые iOS, которые обмениваются данными с серверами Apple для проведения финансовых операций с пользователем через свой идентификатор Apple ID. API-интерфейсы StoreKit являются главным образом касающиеся получение сведений о продукте и проведения транзакций — нет ни один компонент пользовательского интерфейса. Приложения, реализующие покупки из приложений необходимо создать собственный пользовательский интерфейс и отслеживания приобретенных элементов с пользовательским кодом для предоставления пользователю требуется товары или услуги.

Обеспечивает функциональные возможности покупки в приложении требуется несколько этапов:

-  **Настройка приложения** — профиль подготовки для приложения необходимо настроить правильно.
-  **Создание продуктов** — описания продуктов и цены, должны быть созданы на портале iTunes Connect.
-  **Реализация StoreKit** — StoreKit API должен быть реализован в соответствии с типами продуктов, продаваемых.
-  **Создание пользовательского интерфейса и сами продукты** — продукты должен быть реализован, включая механизмы для отслеживания каждой покупки и резервное копирование и восстановление их при необходимости.
-  **Мониторинг продаж и получения средств** — используйте сведения, предоставляемые iTunes Connect для отслеживания доходами Сводная таблица трендов продаж.

В этом документе объясняется, как для выполнения этих действий, чтобы обеспечить покупки из приложений с помощью Xamarin.iOS.

## <a name="requirements"></a>Требования

Для поддержки покупки из приложений с помощью Xcode 7 и более поздних версий необходимо использовать Xamarin.iOS 5.0 или более поздней версии.

## <a name="contents"></a>Описание

 * [Основные сведения о покупке из приложения и ее конфигурация](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

 * [Общие сведения о StoreKit и извлечение сведений о продукте](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

 * [Приобретение готовых к использованию продуктов](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

 * [Приобретение неготовых к использованию продуктов](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

 * [Транзакции и проверки](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

 * [Подписки и отчетность](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>Сводка

В этой статье представлена концепция покупки из приложений, описано, как настроить приложение таким образом, чтобы воспользоваться его преимуществами и представленные примеры, с помощью Xamarin.iOS. Он подробно рассматривается:

-  **Портал подготовки iOS** — рекомендации по обеспечению в приложении приобрести функциональные возможности.
-  **iTunes Connect** — Настройка продуктов для продажи в вашем приложении.
-  **Store Kit** — объяснение классы, используемые для создания функции покупки в приложении.
-  **Кодирование приложения за приобретение** — примеры создания Покупка из приложения в приложение Xamarin.iOS.
-  **Reporting** — Общие сведения по статистике, доступных через iTunes Connect.


## <a name="related-links"></a>Связанные ссылки

- [InAppPurchaseSample](https://developer.xamarin.com/samples/StoreKit/)
- [В руководство по программированию на покупку приложения](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [Руководство по iTunes Connect для разработчиков](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [Ссылка на платформу Kit Store](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Идентификаторы продукта покупки в приложении Q & A](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Техническое Примечание Покупка из приложения](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [Вашу заявку на первый App Store](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Центр ресурсов App Store](https://developer.apple.com/appstore/index.html)
- [Советы по отправке в App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Руководство по оценке приложений App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Управление приложениями](https://developer.apple.com/appstore/resources/managing/index.html)
