---
title: Привязка библиотек Mac для Xamarin.Mac
description: Этот документ содержит ссылки на руководства, описывающие способы работы с привязками Objective-C в приложении Xamarin.Mac, включая Sharpie цели и примеры кода.
ms.prod: xamarin
ms.assetid: 521707CD-79D3-488A-84CB-A37EBF93AC94
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 01/13/2017
ms.openlocfilehash: fde21b2056d56cbf1c4768b287e29f559390f500
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107304"
---
# <a name="binding-mac-libraries-for-xamarinmac"></a>Привязка библиотек Mac для Xamarin.Mac

Используйте следующие ссылки, чтобы узнать о привязке библиотек Objective-C в Xamarin.Mac.

- [**Общие сведения о** ](~/cross-platform/macios/binding/overview.md) -
  описан принцип действия привязки.
- [**Привязка библиотек Objective-C** ](~/cross-platform/macios/binding/objective-c-libraries.md) -
  инструкции о том, как выполнить привязку библиотеки Objective-C для использования в проектах Xamarin.
- [**Введите справочное руководство по определению** ](~/cross-platform/macios/binding/binding-types-reference.md) -
  описывает все атрибуты, доступные для авторов привязки для управления процессом создания привязки.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Цели Sharpie является средством командной строки для начальной загрузки при первом проходе привязки.
Он работает путем анализа файлов заголовков из собственной библиотеки для сопоставления открытого API-интерфейса в [определения привязки](~/cross-platform/macios/binding/binding-types-reference.md) (этот процесс, в противном случае выполняется вручную). Цели Sharpie не обеспечивает привязку, сам по себе, но он может помочь приступить к работе!

## <a name="examples"></a>Примеры

Ссылаться на [пример XMBindingExample Mac](https://github.com/xamarin/mac-samples/tree/master/XMBindingExample) чтобы узнать, как создать привязку Mac с помощью проектов привязки.

## <a name="related-links"></a>Связанные ссылки

- [Привязка Objective-C](~/cross-platform/macios/binding/index.md)
- [Привязка библиотек iOS](~/ios/platform/binding-objective-c/index.md)
