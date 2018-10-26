---
title: Жесты Xamarin.Forms
description: В этом руководстве объясняется, как средства распознавания жестов Xamarin.Forms можно использовать для определения взаимодействия с пользователем с представлениями в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
ms.openlocfilehash: 33968fb935e8b69736ac338bfa0479e4f278e64a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106186"
---
# <a name="xamarinforms-gestures"></a>Жесты Xamarin.Forms

_Средства распознавания жестов можно использовать для определения взаимодействия с пользователем с представлениями в приложении Xamarin.Forms._

Xamarin.Forms [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer) класс поддерживает tap, жестом сжатия, сдвига и жесты прокрутки на [ `View` ](xref:Xamarin.Forms.View) экземпляров.

## <a name="adding-a-tap-gesture-recognizertapmd"></a>[Добавление распознавателя жестов касания](tap.md)

Используется для обнаружения tap жеста касания и распознается с [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) класса.

## <a name="adding-a-pinch-gesture-recognizerpinchmd"></a>[Добавление распознавателя жестов жестом сжатия](pinch.md)

Жест жестом сжатия используется для выполнения интерактивного масштабирования и распознается с [ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer) класса.

## <a name="adding-a-pan-gesture-recognizerpanmd"></a>[Добавление распознавателя жестов pan](pan.md)

Жест pan используется для обнаружения движения пальца по экрану и применения к содержимому, перемещение и распознается с [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) класса.

## <a name="adding-a-swipe-gesture-recognizerswipemd"></a>[Добавление распознавателя жестов проведите по экрану](swipe.md)

Жест прокрутки происходит, когда палец перемещается по экрану в горизонтальном или вертикальном направлении, а также часто используется для инициации перехода по содержимому. Распознает жесты прокрутки с [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) класса.
