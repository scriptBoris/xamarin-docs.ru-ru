---
title: Создание пользовательских интерфейсов в конструкторе IOS
description: В этом документе описывается, как использовать конструктор Xamarin для iOS для построения интерфейса пользователя приложения с раскадровками и xib-файлов. Он содержит ссылки на документы, которые обсуждаются доступность средств, его основные функциональные возможности, проектирование элементы управления и приводятся примеры его использования.
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/31/2018
ms.openlocfilehash: 7c6529c539ab502fb6c13226acd18a57f8f6d58e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109748"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>Создание пользовательских интерфейсов в конструкторе IOS

_Конструктор Xamarin для iOS — это визуальный конструктор для iOS форматы раскадровки и конструкторе Interface Builder, полностью интегрированную с помощью Visual Studio для Mac и Visual Studio. Конструктор iOS поддерживает полной совместимости с форматами раскадровки и файлов xib, поэтому файлы можно редактировать в Visual Studio для Mac или Visual Studio Помимо конструктора Interface Builder. Кроме того конструктор Xamarin для iOS поддерживает дополнительные функции, такие как пользовательские элементы управления, которые отображаются во время разработки в редакторе._

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![iOS Designer в Visual Studio для Mac](images/designer-vsmac-sml.png "конструктора iOS")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Конструктор в Visual Studio iOS](images/designer-vs.png "конструктора iOS")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>Доступность

Xamarin для iOS используется доступно в Visual Studio для Mac и Visual Studio 2017 в Windows.

Эти руководства предполагается Знакомство с содержимым, охваченных [Xamarin.iOS Приступая к работе проводит](~/ios/get-started/index.md).

## <a name="ios-designer-basicsintroductionmd"></a>[Основные сведения о конструкторе iOS](introduction.md)

В этом руководстве описываются функции конструктора iOS Xamarin. Здесь рассматриваются основы конструктора, показывающий, как использовать конструктор для визуального размещения элементов управления и изменение свойств.

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[Обзор разработки элементов управления](ios-designable-controls-overview.md)

В этом руководстве выполняет поиск в глубину в пользовательские элементы управления, как они создаются и какие требования они должны соответствовать для отображения в рабочей области конструирования. Кроме того показано, как для отладки распространенных проблем, которые могут возникнуть при использовании разработки элементов управления.

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[Пошаговое руководство. Использование пользовательских элементов управления в конструкторе IOS](ios-designable-controls-walkthrough.md)

Эта статья содержит пошаговое руководство, в котором показано, как создать пользовательский элемент управления и использовать его в конструктор iOS. В этом примере показано создание элемента управления в инструментарий разработчика, его можно перетаскивания или перетащить в представление. Кроме того показано, как реализовать элемент управления, поэтому отображается правильно во время разработки и среды выполнения, а также как создать свойства, которые могут быть заданы во время разработки.

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[Автоматический макет в конструкторе IOS Xamarin](designer-auto-layout.md)

Это руководство описывает iOS автоматический макет и новые ограничения рабочего процесса, доступного в конструкторе iOS.
