---
title: Связи Haptic в Xamarin.iOS
description: В этом документе описывается, как для обеспечения обратной связи haptic в приложении Xamarin.iOS. В нем описывается UIImpactFeedbackGenerator UINotificationFeedbackGenerator и UISelectionFeedbackGenerator.
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: b2c381c59ba1574e80babc2c7e68535a3deffe35
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123132"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>Связи Haptic в Xamarin.iOS

<a name="Overview" />

## <a name="overview"></a>Обзор

На iPhone 7 и iPhone 7 и Apple включила haptic ответы, предоставляющие дополнительные способы физически взаимодействовать пользователь. Обеспечение обратной связи haptic (часто называют просто Haptics) использует смысле сенсорного ввода (через force, вибрация или перемещения) в пользовательском интерфейсе конструктора. Используйте эти новые параметры tactile отзывов для привлечет внимание пользователя и подчеркивает их действия.

Будут подробно рассмотрены следующие темы:

- [Об обратной связи Haptic](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>Об обратной связи Haptic

Несколько встроенных элементов пользовательского интерфейса уже обеспечивают связи haptic, такие как средства выбора, коммутаторы и ползунков. iOS 10 теперь добавлена возможность программно запускать с помощью конкретный подкласс haptics `UIFeedbackGenerator` класса.

Разработчик может использовать одно из следующих `UIFeedbackGenerator` подклассам программно триггера обратной связи haptic:

- `UIImpactFeedbackGenerator` — Используйте этот генератор обратной связи в дополнение к действие или задачи, такие как представления «thud» при представлении слайдов в месте, или если два на экране объекта конфликтуют.
- `UINotificationFeedbackGenerator` — Используйте этот генератор обратной связи для получения уведомлений, таких как завершение, сбои или любой другой тип предупреждения.
- `UISelectionFeedbackGenerator` — Используйте этот генератор обратной связи для выделения активно изменение, например выбора элемента из списка.

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

Используйте этот генератор обратной связи в дополнение к действие или задачи, такие как представления «thud» при представлении слайдов в месте, или если два на экране объекта конфликтуют.

Используйте следующий код, чтобы отзыв влияние триггера:

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

Когда разработчик создает новый экземпляр класса `UIImpactFeedbackGenerator` класса, они предоставляют `UIImpactFeedbackStyle` указание степень обратной связи, как:

- `Heavy`
- `Medium`
- `Light`

`Prepare` Метод `UIImpactFeedbackGenerator` вызывается для информирования системы, что связи haptic будет возникать таким образом, чтобы его можно свести к минимуму задержки.

`ImpactOccurred` Метод затем активирует обратной связи haptic.

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

Используйте этот генератор обратной связи для получения уведомлений, таких как завершение, сбои или любой другой тип предупреждения.

Используйте указанный ниже код для отправки уведомлений триггера:

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

Новый экземпляр класса `UINotificationFeedbackGenerator` создается класс и его `Prepare` метод вызывается для информирования системы, что связи haptic будет возникать таким образом, чтобы его можно свести к минимуму задержки.

`NotificationOccurred` Вызывается для активации обратной связи haptic с заданной `UINotificationFeedbackType` из:

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

Используйте этот генератор обратной связи для выделения активно изменение, например выбора элемента из списка.

Используйте следующий код, чтобы отзывы о выборе триггера:

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

Новый экземпляр класса `UISelectionFeedbackGenerator` создается класс и его `Prepare` метод вызывается для информирования системы, что связи haptic будет возникать таким образом, чтобы его можно свести к минимуму задержки.

`SelectionChanged` Метод затем активирует обратной связи haptic.

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается новых видов связи haptic в iOS 10 и способы их реализации в Xamarin.iOS.

## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
