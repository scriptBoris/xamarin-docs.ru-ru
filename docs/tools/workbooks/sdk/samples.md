---
title: Примеры интеграции
description: Этот документ содержит ссылки на примеры, демонстрирующие Xamarin Workbooks интеграции. Связанные примеры работы с подготовки к просмотру представление и SkiaSharp.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: e35577b116180d2745e2f6afb792547f63873214
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117425"
---
# <a name="sample-integrations"></a>Примеры интеграции

См. в разделе [приемника кухни] [ KitchenSink] образец рабочий пример интеграции. Просто построить `KitchenSink.sln` в Visual Studio для Mac или Visual Studio и откройте `KitchenSink.workbook`.

[![Снимок экрана кухни приемника интеграции:](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

Кухни приемник в нем демонстрируется оба вида понятия:

* Представление части демонстрируется использование `RepresentationManager` для улучшения визуализации с помощью встроенных представлений.
* `Person` Объекта и его связанные визуализации JavaScript демонстрируют использование `ISerializableObject` без прохождения через представление поставщика.

Также см. в разделе [SkiaSharp] [ skiasharp] пример реальной интегрированного решения, которое использует существующую [представления](~/tools/workbooks/sdk/representations.md) предоставляемые Xamarin Workbooks для подготовки к просмотру его типы.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
