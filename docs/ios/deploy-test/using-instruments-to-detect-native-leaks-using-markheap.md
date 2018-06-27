---
title: Профилирование приложений Xamarin.iOS с помощью Instruments
description: Этот документ описывает использование приложения Apple Instruments для профилирования приложения Xamarin.iOS, установленного на устройстве или в симуляторе.
ms.prod: xamarin
ms.assetid: 70A8CAC8-20C2-655B-37C3-ACF9EA7874D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 25129d532af0b146afedf28865649ffc9e38ee17
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785616"
---
# <a name="profiling-xamarinios-applications-with-instruments"></a>Профилирование приложений Xamarin.iOS с помощью Instruments

Xcode **Instruments** — это инструмент, который можно использовать для профилирования приложений Xamarin.iOS на устройстве или в симуляторе. Mono использует его модель JIT для компиляции кода, но Instruments плохо справляется с интерпретацией данных такого типа, что затрудняет работу с выходными данными использующих Instruments приложений на базе симулятора.
По этой причине руководство будет рассматривать интерпретацию выходных данных Instruments с помощью приложения разработчика.

## <a name="requirements"></a>Требования

Xcode Instruments работает только на компьютерах Mac.

## <a name="opening-the-instruments-app"></a>Запуск приложения Instruments

Выберите устройство и запустите приложение Instruments:

1.  Откройте проект Xamarin.iOS в Visual Studio для Mac.
2.  Выберите конфигурацию **Отладка|iPhone**.
3.  Подключите устройство iOS к компьютеру.
4.  В меню **Запуск** выберите пункт **Отправить на устройство**. После этого приложение будет собрано и отправлено на устройство.
5.  В меню **Сервис** выберите пункт **Запустить Instruments**.


Instruments откроется со следующим диалоговым окном:

 [![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png "Выбор шаблона профилирования")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png#lightbox)

Щелкните шаблон **Allocations** (Распределения). Вы можете использовать и другие шаблоны, однако в этой статье рассматривается только шаблон профиля **Allocations**.

Теперь выберите устройство и приложение в меню в верхней части окна:

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png "Выбор устройства и приложения")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png#lightbox)

Выберите устройство iOS в меню в верхней части окна приложения, а рядом с ним — приложение, для которого требуется профилирование (на снимке экрана выше это **MemoryDemo**).

Если вашего устройства нет в меню, проверьте, нет ли в **Консоли** Visual Studio для Mac сообщений об ошибках, которые могут появляться при развертывании приложения на устройство. Также проверьте подготовку устройства для разработки в Xcode Organizer.

Нажмите кнопку **Choose** (Выбрать). Откроется следующий экран:

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png "Интерфейс профилирования")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png#lightbox)

Чтобы начать профилирование, нажмите кнопку записи (красный кружок в левом верхнем углу).

На следующем снимке экрана показан пример профилирования с помощью **Instruments**:

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png "Пример профилирования с помощью Instruments")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png#lightbox)

## <a name="summary"></a>Сводка

В этом руководстве было показано, как запустить Xcode Instruments для мониторинга приложения iOS в среде Visual Studio для Mac. Пример использования Instruments для диагностики проблем с памятью см. в [Пошаговом руководстве по работе с Instruments](~/ios/deploy-test/walkthrough-apples-instrument.md).

## <a name="related-links"></a>Связанные ссылки

- [Пошаговое руководство по работе с Instruments](~/ios/deploy-test/walkthrough-apples-instrument.md)
- [Сборка мусора Xamarin.iOS](https://krumelur.me/2015/04/27/xamarin-ios-the-garbage-collector-and-me/)
