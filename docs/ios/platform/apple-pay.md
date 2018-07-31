---
title: Apple Pay в Xamarin.iOS
description: В этом руководстве рассматривается настройка среды для использования с Apple Pay, чтобы оплачивать физические товары, например качества пищевых продуктов, развлечения и в группах с помощью приложения Xamarin.iOS. Он содержит сведения о необходимых идентификаторы, сертификаты и прав.
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/05/2017
ms.openlocfilehash: f2a38a4305aa11c78f3e4e35265a86dc71642777
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351669"
---
# <a name="apple-pay-in-xamarinios"></a>Apple Pay в Xamarin.iOS

_В этом руководстве рассматривается настройка среды для использования с Apple Pay, чтобы оплачивать физические товары, например качества пищевых продуктов, развлечения и в группах с помощью приложения Xamarin.iOS. Он содержит сведения о необходимых идентификаторы, сертификаты и прав._

Apple Pay была введена вместе с iOS 8, что позволяет пользователям оплачивать физические товары, таких как еда, развлечения и в группах через свои устройства с iOS. Он доступен на iPhone 6 "и" iPhone 6 плюс, а также может быть связан с Apple Watch за покупки в магазине. При использовании на устройстве iPhone, он использует Touch ID как способ подтверждения и авторизовать транзакций для пользователя кредитной или дебетовой карты.

## <a name="requirements"></a>Требования

Apple Pay доступна только в iOS 8 и более поздних версий и поэтому требуется как минимум Xcode 6.

Следующие элементы необходимы также для интеграции Apple Pay в ваше приложение.

 - Платформа обработчика платежей
 - Коммерческий идентификатор
 - Сертификат Apple Pay
 - Назначение Apple Pay

В этом документе мы рассмотрим эти элементы более подробно.

## <a name="differences-between-apple-pay-and-iap"></a>Различия между Apple Pay и IAP

Основное различие между Apple Pay и *покупки из приложений* (IAP), относится к продуктам, которые они продают. *Физический* продаже товаров через Apple Pay; качества пищевых продуктов, услуги размещения и физического развлечения (таких как билеты кинотеатрах) являются примерами это. Напротив, продает IAP *виртуального* товаров, например "премиум" или дополнительное содержимое и подписок — обработки дополнительных месяцев, служба потоковой передачи или дополнительных жизнь в игре.

Платформы на также являются ключевое различие; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) — используется для Apple Pay, а [StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) предоставляет платформу API для IAP.

С помощью Apple Pay, Apple [состояний](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) что он «взимается плата пользователей, продавцов и разработчиков, для использования Apple Pay для платежей». В отличие от этого IAP имеет плата 30% для каждой транзакции. Кроме того, с помощью Apple Pay, транзакции не проходит через Apple вообще, вместо этого он проходит через платформа платежей.

## <a name="using-a-payment-processor-platform"></a>С помощью платформы процессора оплаты

Одним из фундаментальных части Apple Pay является обработка платежей. Хотя можно сделать это самостоятельно, требуются значительные знания криптографии
- как описано в Apple [обработки платежей, руководство по](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html).
Платформ обработки платежей, с другой стороны, обработки этих операций для вас, позволяя сосредоточиться на создании приложения.

Две следующие варианты:

- **Stripe** -Зарегистрируйтесь на [Stripe.com](https://stripe.com/) для доступа к своим API.

- **Как JudoPay** -извлечь их [Xamarin пример кода на github](https://github.com/Judopay/Xamarin-Sample-App)и зарегистрироваться можно по адресу [JudoPay.com](https://www.judopay.com/).

## <a name="provisioning-for-apple-pay"></a>Подготовка для Apple Pay

Настройка приложения для использования Apple Pay требует установки на портале разработчика Apple и в приложении. Существует ряд действий, которые следует выполнить для успешной инициализации приложения для Apple pay:

1. Создайте ИД продавца:
    - Выполните действия, [здесь](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. Создать ИД приложения с возможностями Apply Pay и добавить в него ИД продавца:
    - Выполните действия, [здесь](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. Создайте сертификат для ИД продавца:
    - Выполните действия, [здесь](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. Создайте профиль подготовки с только что созданный идентификатор приложения:
    - Выполните действия, [здесь](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. Добавьте назначения Apple Pay:
    - Выберите Apple pay назначением, как описано [здесь](~/ios/deploy-test/provisioning/entitlements.md), или вручную добавить пару "ключ/значение" к файлу из [здесь](~/ios/deploy-test/provisioning/entitlements.md)

## <a name="working-with-apple-pay"></a>Работа с Apple Pay

Apple внесла ряд усовершенствований для Apple Pay в iOS 10, которые позволяют пользователю для выполнения безопасных платежей с веб-сайтов и при взаимодействии с помощью Siri и карт.

В iOS 10 ряд новых интерфейсов API добавлены, работающие с iOS и watchOS для поддержки динамического оплаты сетей и новую тестовую среду "песочницы".

### <a name="apple-pay-website-integration"></a>Интеграция веб-сайте Apple Pay

Новое в iOS 10, разработчик может включать Apple Pay непосредственно в своих веб-сайтов с помощью **ApplePay JS**. Просмотр веб-сайта с помощью Safari в iOS или macOS можно выполнять платежи с Apple Pay, проверяя транзакции на их устройствах iPhone или Apple Watch. Дополнительные сведения см. в разделе Apple [ссылке ApplePay JP платформы](https://developer.apple.com/reference/applepayjs).

### <a name="passkit-framework-enhancements"></a>Усовершенствования PassKit Framework

В iOS 10, PassKit framework была расширена для поддержки Apple Pay за пределами `UIKit` и позволить издателей карты представить свои собственные карточек в своих приложениях.


#### <a name="supporting-apple-pay-outside-of-uikit"></a>Поддержка Apple Pay за пределами UIKit

С помощью [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) и [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), приложение может поддерживать же функциональность, предоставляемую [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) без использования UIKit. Хотя этот новый API для поддержки Apple Pay на Apple Watch (и в конкретных целей также), он является необязательным в других ситуациях (например, существующие приложения). Тем не менее Apple предлагает, как можно скорее переходе на новый API для предоставления широкой поддержки Apple Pay во всех приложениях разработчика с единой базой кода. Дополнительные сведения о намерения и интеграция Siri, см. в нашей [введение в SiriKit](~/ios/platform/sirikit/index.md) документации.

#### <a name="presenting-issuer-cards-from-within-apps"></a>Представления карты издателя из внутри приложений

С iOS 10 PassKit платформы, разрешить поставщиков карты для представления карты из в свои собственные приложения были добавлены новые функции. Разработчик может добавить `PKPaymentButtonTypeInStore` UIButton для приложения пользовательский интерфейс, который будет отображать кнопку Apple Pay для карты.

`PresentPaymentPass` Метод [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) класс также может использоваться для программного отображения карты.

### <a name="new-payment-network-support"></a>Поддержка новых сетевых оплаты

Новое в iOS 10, приложение может автоматически поддерживать новую сеть оплаты после его появления избавляет разработчика от необходимости изменять, перекомпилируйте приложение и отправьте его в App Store.

Новый [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) метод `PKPaymentNetwork` класс позволяет приложению для обнаружения сетей, доступных на устройстве пользователя во время выполнения. Кроме того [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) свойство была расширена вступили в качестве аргумента имя поставщика платежных услуг. С помощью этих методов, приложение может автоматически поддерживать какой-либо сети, которая поддерживает оплаты поставщика.

Дополнительные сведения см. в разделе наших [платить конфигурации Apple](~/ios/platform/apple-pay.md) и Apple [платить руководстве Apple](https://developer.apple.com/apple-pay/).

### <a name="new-testing-environment"></a>Новые среды тестирования

С iOS 10 Apple представила новую среду тестирования, которая позволяет разработчику для подготовки тестового платежных карт непосредственно на устройстве iOS. Этой новой среды тестирования, затем возвращает зашифрованные теста платежные данные в приложение.

Чтобы включить новую среду тестирования, сделайте следующее:

1. Создайте новый тестирования iCloud учетной записи в iTunes Connect.
2. Войдите на устройство iOS с помощью новой учетной записи для тестирования.
3. Задайте нужный регион, чтобы протестировать приложение в.
4. Используйте один из тестов платежных карт из [платить руководстве Apple](https://developer.apple.com/apple-pay/) оплаты.

> [!IMPORTANT]
> Переключившись учетные записи iCloud, устройство автоматически переключается на новую среду тестирования. Тем не менее, по-прежнему Apple **требует** приложение, чтобы протестировать с помощью реального карточки в рабочей среде перед отправкой в iTunes App Store.

## <a name="summary"></a>Сводка

В этой статье мы изучили различные элементы, необходимые для использования Apple Pay в приложении. Мы рассмотрели способы создания ИД продавца и как она используется в **Entitlements.plist**, который необходимо изменить вручную.

## <a name="related-links"></a>Связанные ссылки

- [Покупки из приложений](~/ios/platform/in-app-purchasing/index.md)
- [Введение в PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (пример)](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
