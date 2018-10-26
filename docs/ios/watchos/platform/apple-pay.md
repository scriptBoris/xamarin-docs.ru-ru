---
title: Apple Pay в watchOS в Xamarin
description: В этой статье рассматриваются усовершенствования Apple сделала для Apple Pay в watchOS 3, а также способы их реализации в Xamarin.iOS для Apple Watch.
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 354e03ee1e07ba99fcdeb05617bc65ed89f0e8c2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103092"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay в watchOS в Xamarin

Apple внесла ряд усовершенствований для Apple Pay в watchOS 3, обеспечивающее поддержку платежей в приложении. Это позволяет безопасно предоставить оплаты и контактные данные для оплаты физических товаров и услуг непосредственно из Apple Watch.


## <a name="about-apple-pay-enhancements"></a>Об улучшениях Apple Pay

Как Stated выше Apple внесла ряд усовершенствований для Apple Pay в watchOS 3, позволяющую для безопасных платежей и контактные данные для оплаты физических товаров и услуг непосредственно из Apple Watch. Эти усовершенствования предоставляются с изменениями в PassKit Framework.

В iOS 10 и watchOS 3 ряд новых интерфейсов API добавлены, работающие с iOS и watchOS для поддержки динамического оплаты сетей и новую тестовую среду "песочницы".

## <a name="passkit-framework-enhancements"></a>Усовершенствования PassKit Framework

В iOS 10, PassKit framework была расширена для поддержки Apple Pay за пределами `UIKit` и поставщиков карты для представления карты из в своих приложениях. 

### <a name="supporting-apple-pay-outside-of-uikit"></a>Поддержка Apple Pay за пределами UIKit

С помощью [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) и [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), приложение может поддерживать же функциональность, предоставляемую [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) без использования UIKit. Хотя этот новый API для поддержки Apple Pay на Apple Watch (и в конкретных целей также), он является необязательным в других ситуациях (например, существующие приложения). Тем не менее Apple предлагает, как можно скорее переходе на новый API для предоставления широкой поддержки Apple Pay во всех приложениях разработчика с единой базой кода. Дополнительные сведения о намерения и интеграция Siri, см. в нашей [введение в SiriKit](~/ios/platform/sirikit/index.md) документации.

### <a name="presenting-issuer-cards-from-within-apps"></a>Представления карты издателя из внутри приложений

С iOS 10 и watchOS 3 были добавлены новые возможности платформы PassKit, разрешить поставщиков карты для представления своих платежных карт в собственных приложениях. Разработчик может добавить `PKPaymentButtonTypeInStore` UIButton для приложения пользовательский интерфейс, который будет отображать кнопку Apple Pay для карты.

`PresentPaymentPass` Метод [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) класс также может использоваться для программного отображения карты.

## <a name="new-payment-network-support"></a>Поддержка новых сетевых оплаты

Новое для iOS 10 и watchOS 3, приложение может автоматически поддерживать новую сеть оплаты после его появления избавляет разработчика от необходимости изменять, перекомпилируйте приложение и отправьте его в App Store.

Новый [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) метод `PKPaymentNetwork` класс позволяет приложению для обнаружения сетей, доступных на устройстве пользователя во время выполнения. Кроме того [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) свойство была расширена вступили в качестве аргумента имя поставщика платежных услуг. С помощью этих методов, приложение может автоматически поддерживать какой-либо сети, которая поддерживает оплаты поставщика.

Дополнительные сведения см. в разделе наших [платить конфигурации Apple](~/ios/platform/apple-pay.md) и Apple [платить руководстве Apple](https://developer.apple.com/apple-pay/).

## <a name="new-testing-environment"></a>Новые среды тестирования

В iOS 10 и watchOS 3 Apple представила новую среду тестирования, которая позволяет разработчику для подготовки тестового платежных карт непосредственно на устройстве iOS. Этой новой среды тестирования, затем возвращает зашифрованные теста платежные данные в приложение.

Чтобы включить новую среду тестирования, сделайте следующее:

1. Создайте новый тестирования iCloud учетной записи в iTunes Connect.
2. Войдите на устройство iOS с помощью новой учетной записи для тестирования.
3. Задайте нужный регион, чтобы протестировать приложение в.
4. Используйте один из тестов платежных карт из [платить руководстве Apple](https://developer.apple.com/apple-pay/) оплаты.

> [!NOTE]
> Переключившись учетные записи iCloud, устройство автоматически переключается на новую среду тестирования. Тем не менее, по-прежнему Apple **требует** приложение, чтобы протестировать с помощью реального карточки в рабочей среде перед отправкой в iTunes App Store.

## <a name="summary"></a>Сводка

Статья описывает усовершенствования Apple сделала для Apple Pay в watchOS 3, а также способы их реализации в Xamarin.iOS.
