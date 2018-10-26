---
title: Специальные возможности в Xamarin.Forms
description: Построение приложения доступны гарантирует, что приложение будет доступен для тех, кто подход с различными потребностями и возможности пользовательского интерфейса.
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: ac0ffbdce6b0c55e8ad9d774d80e3d9b8bf84089
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116450"
---
# <a name="xamarinforms-accessibility"></a>Специальные возможности в Xamarin.Forms

_Построение приложения доступны гарантирует, что приложение будет доступен для тех, кто подход с различными потребностями и возможности пользовательского интерфейса._

Повышение уровня доступности приложения Xamarin.Forms означает думать о макета и внешнего вида многие элементы пользовательского интерфейса. Рекомендации, которые следует учитывать, см. в разделе [контрольный список специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md). Многие детали реализации специальных возможностей, например крупный шрифт и подходящие настройки цвета и контрастности уже можно работать посредством API-интерфейсов в Xamarin.Forms.

[Android специальных возможностей](~/android/app-fundamentals/accessibility.md) и [специальных возможностей iOS](~/ios/app-fundamentals/accessibility.md) руководства содержат сведения о собственных API, предоставляемые Xamarin и [руководство специальных возможностей универсальной платформы Windows на сайте MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) объясняет собственный подход на этой платформе. Эти API используются для полной реализации приложений со специальными возможностями на каждой платформе.

Xamarin.Forms в данный момент нет *встроенные* поддерживают для всех специальных возможностей API, доступных на каждом из базовых платформ. Тем не менее он поддерживает задание свойств службы автоматизации в элементах пользовательского интерфейса для поддержки средств помощь по экрана средство чтения и навигации, которое является одним из самых важных компонентов для создания приложений со специальными возможностями. Дополнительные сведения см. в разделе [свойства автоматизации](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md).

Можно также в приложениях Xamarin.Forms есть последовательности табуляции элементов управления указан. Дополнительные сведения см. в разделе [с помощью клавиатуры](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md).

Другие интерфейсы API специальных возможностей (таких как [PostNotification в iOS](~/ios/app-fundamentals/accessibility.md)) может лучше подходить к [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) или [пользовательского средства визуализации](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) реализации. Они не рассматриваются в данном руководстве.

## <a name="testing-accessibility"></a>Тестирование специальных возможностей

Приложения Xamarin.Forms обычно нескольких платформ, это означает, что тестирование специальных возможностей в соответствии с платформой. Используйте следующие ссылки, чтобы узнать, как для проверки специальных возможностей на каждой платформе.

- [**iOS тестирования**](~/ios/app-fundamentals/accessibility.md)
- [**Android тестирования**](~/android/app-fundamentals/accessibility.md)
- [**AccScope Windows (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>Связанные ссылки

- [Кросс платформенных специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md)
- [Свойства автоматизации](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [Специальные возможности клавиатуры](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)
