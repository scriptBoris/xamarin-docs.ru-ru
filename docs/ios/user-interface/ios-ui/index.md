---
title: Пользовательские интерфейсы в iOS
description: Этот документ содержит ссылки на руководства, описывающие способы создания пользовательских интерфейсов в приложении Xamarin.iOS. Связанные руководства рассматриваются API внешнего вида, создание объектов пользовательского интерфейса, параметры макета и многое другое.
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: efb88ada8a4b4c36dd49de137eb64acd63552968
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115683"
---
# <a name="user-interfaces-in-ios"></a>Пользовательские интерфейсы в iOS

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[API внешнего вида](introduction-to-the-appearance-api.md)

iOS позволяет многие визуальные атрибуты элементов управления пользовательского интерфейса тематического, с помощью API-интерфейсы UIAppearance.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[Создание объектов пользовательского интерфейса](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Apple группы связанных части функциональных возможностей в «платформы», которые приравниваются к пространствам имен Xamarin.iOS. `UIKit` — Это пространство имен, которое включает все элементы интерфейса пользователя для iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[Параметры макета](~/ios/user-interface/ios-ui/layout-options.md)

Существует два различных механизма контроля размещения, когда размер или поворачивать представления: автоматическое изменение размера и Автомакет.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[Обеспечение обратной связи Haptic](~/ios/user-interface/ios-ui/haptic-feedback.md)

В этой статье рассматриваются новые типы связи haptic в iOS 10 и способы их реализации в Xamarin.iOS.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[Работа с потоком пользовательского интерфейса](~/ios/user-interface/ios-ui/ui-thread.md)

Код следует добавлять только поток изменений в элементы управления интерфейса из главного пользователя (или пользовательского интерфейса). Обновления интерфейса пользователя, которые происходят в другом потоке (например, обратный вызов или фоновый поток) не может получить отображен на экране, или даже может привести к сбою.




