---
title: Изменения платформы дополнительных watchOS 3
description: В этом документе описываются различные framework изменения, внесенные в watchOS 3 и способы работы с ними в Xamarin. Рассматриваются основные данные, Core движения, Foundation, HealthKit, HomeKit, PassKit и UIKit.
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 745c39dab1f73870ce036791434ed9a0b05d681b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122625"
---
# <a name="additional-watchos-3-frameworks-changes"></a>Изменения платформы дополнительных watchOS 3

_В этой статье рассматриваются дополнительные, незначительные изменения и улучшения для существующих платформ для watchOS 3._

Помимо основных изменений для iOS Apple сделала изменения и усовершенствования для нескольких существующих платформ в watchOS 3.


## <a name="core-data"></a>Основные данные

В framework Core Data для watch OS 3 быть внесены следующие улучшения:

- Корневой [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) объектов поддерживает параллельные завершение со сбоем и выборка без сериализации.
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) класс поддерживается пул хранилищ данных SQLite.
- [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) объектов с хранилищами данных SQLite в режиме журнал WAL поддержки нового поколения запроса компонентов, где управляемый объект контекстов (MOC) можно прикрепить к версии конкретной базы данных для будущих выборки и Ошибка транзакции.
- С помощью высокоуровневым `NSPersistenceContainer` ссылка `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) и другим ресурсам конфигурации Core Data.
- Были добавлены несколько новых удобных методов `NSManagedObject` облегчая процесс для выполнения операций выборки и создание подклассов.

Дополнительные сведения см. в разделе Apple [справочные материалы по основной данных Framework](https://developer.apple.com/reference/coredata).


## <a name="core-motion"></a>Core движения

В framework Core движения для watch OS 3 быть внесены следующие улучшения:

- Новое событие движения устройство использует акселерометр и гироскоп для предоставления обновлений движения и ориентацию. приложение можно зарегистрировать для данного обновления (по ставкам до 100 Гц).
- Новое событие Pedometer обеспечивает уведомления в реальном времени, когда пользователь приостанавливает и возобновляет выполнение. Используйте [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) для регистрации событий pedometer обычном или фоновом режиме.


## <a name="foundation"></a>Foundation

Следующие улучшения были внесены в Framework Foundation для watch OS 3:

- Используйте новый [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) класса, чтобы дата и время вычисления интервала, например длительности для сравнения интервалы и тестирование для пересечения интервал.
- Были добавлены несколько новых свойств [NSLocal](https://developer.apple.com/reference/foundation/nslocale) класс получения местной информации и отображение доступных форматов.
- Используйте новый [NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement) класс для преобразования между разные единицы из мер (единица Измерения) или выполняют вычисления над значениями в разных UOMs.
- Используйте новый [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) класс для форматирования локализованного измерения для отображения для конечного пользователя.
- Используйте новый [NSUnit](https://developer.apple.com/reference/foundation/nsunit) и [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) классы для представления определенных UOMs.


## <a name="healthkit"></a>HealthKit

Следующие улучшения были внесены в HealthKit Framework для watch OS 3:

- Используйте новый [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) класс для указания `ActivityType` и `LocationType` для тренировок.
- Новый [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) и `WheelchairUse` метод [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) класса были добавлены для работы с колясок связанные данные о работоспособности.
- Были добавлены новые ключи метаданных для типов погоды (такие как `HKWeatherConditionClear` и `HKWeatherConditionCloudy`) и для организации тренировок типов (таких как `HKWorkoutActivityTypeFlexibility` и `HKWorkoutActivityTypeWheelchairRunPace`) были добавлены.


## <a name="homekit"></a>HomeKit

В фреймворк HomeKit для watch OS 3 быть внесены следующие улучшения:

- Добавлена возможность просматривать и взаимодействовать с HomeKit подключения IP-адрес камеры.
- Добавлены несколько новых служб и характеристики.
- Добавлены дополнительные контекста и конфигурации аксессуаров первичная, обслуживает и свяжите службы.


## <a name="passkit"></a>PassKit

Следующие улучшения были внесены в PassKit Framework для watch OS 3:

- При развертывании этой среды для поддержки безопасных платежей в приложении на Apple Watch из физических товаров и услуг.
- Теперь доступны следующие классы: [PKPayment](https://developer.apple.com/reference/passkit/pkpayment), [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod), [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) и [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

В платформе UIKit для watch OS 3 имеют внесены следующие улучшения.

- Для поддержки динамического типа в метки, текстовые поля и текстовые поля используйте новый `PreferredFontForTextStyle` метод `UIFont` класса.
- `ColorWithDisplayP3` Метод была добавлена для поддержки широкая цветовая палитра.


## <a name="related-links"></a>Связанные ссылки

- [Приступая к работе (пример)](https://developer.xamarin.com/samples/monotouch/WatchKit/)
- [Новые возможности в watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
