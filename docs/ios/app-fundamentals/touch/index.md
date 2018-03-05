---
title: "Сенсорный ввод"
description: "Сенсорных экранов на многих современных устройств позволяет пользователям быстро и эффективно взаимодействовать с устройствами естественным и интуитивно понятным способом. Такое взаимодействие не ограничивается только обнаружение простых touch — можно использовать жесты также. Например масштабирование сжатием жестов является очень распространенным примером этого — сжатия часть экрана с двумя пальцами, которые пользователь может увеличить или уменьшить. В этом руководстве рассматриваются сенсорный ввод и жестов в iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4A17FD28-313F-4AAC-B82B-3847B4D64A88
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/23/2017
ms.openlocfilehash: 8f6c26048bc0ece0d64acf069151ff1d67403ccc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2018
---
# <a name="touch"></a>Сенсорный ввод

_Сенсорных экранов на многих современных устройств позволяет пользователям быстро и эффективно взаимодействовать с устройствами естественным и интуитивно понятным способом. Такое взаимодействие не ограничивается только обнаружение простых touch — можно использовать жесты также. Например масштабирование сжатием жестов является очень распространенным примером этого — сжатия часть экрана с двумя пальцами, которые пользователь может увеличить или уменьшить. В этом руководстве рассматриваются сенсорный ввод и жестов в iOS._


Как и других мобильных платформ iOS имеет ряд способов обработки сенсорный ввод. Может поддерживать мультисенсорные — много точек контакта на экране и сложные жесты. В этом руководстве описаны некоторые основные принципы, а также particularities реализации сенсорный ввод и жестов в iOS.

iOS инкапсулирует сенсорного ввода данных в `UITouch` класс, который доступен для приложений с помощью ряда `UIResponder` методы. Приложения можно переопределить эти методы в подклассах `UIView` и `UIViewController`, которые являются производными от `UIResponder`.

Помимо журналов сенсорный ввод данных, операций ввода-вывода предоставляет средства для интерпретации шаблоны штрихи в жесты. В свою очередь эти распознавателей жестов можно использовать для интерпретации команд конкретного приложения, например Поворот изображения или выключите страницы. iOS предоставляет широкий набор классов для обработки распространенных жесты с минимальным добавленный код.

Выбор между штрихов и распознавателей жестов можно путаницу. В этом руководстве, Майкрософт рекомендует, как правило, предпочтений должен быть предоставлен для распознавателей жестов. Распознаватель жестов реализуются как дискретные классы, обеспечивающие высокая степень разделения проблем и улучшенную инкапсуляцию. Это позволяет совместно использовать логику между различными представлениями, что сводит к минимуму объем кода, написанного.

Однако бывают случаи, когда требуется использовать обработку низкоуровневые сенсорный ввод и даже отслеживать несколько пальцами, например, чтобы создать программу finger-paint.

## <a name="sections"></a>Разделы

-  [Touch в iOS](touch-in-ios.md)
-  [Пошаговое руководство: Использование сенсорного ввода в iOS](ios-touch-walkthrough.md)
-  [Мультисенсорные отслеживания](touch-tracking.md)

Данное руководство служит Знакомство с сенсорного ввода в iOS. Дополнительные сведения об использовании 3D Touch и Haptic отзывов в iOS которой впервые появились в iOS 9 и 10 соответственно см. в определенных направляющих ниже:

* [3D Touch](~/ios/platform/3d-touch.md)
* [Обратная связь Haptic](~/ios/user-interface/ios-ui/haptic-feedback.md)



## <a name="related-links"></a>Связанные ссылки

- [iOS Touch запуска (пример)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS Touch окончательного (пример)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
- [FingerPaint (пример)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)