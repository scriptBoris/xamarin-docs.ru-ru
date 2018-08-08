---
title: Строки и пути SkiaSharp
description: В этой статье описывается использование SkiaSharp Рисование линий и контуры в приложениях Xamarin.Forms и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9febfabb7b44b1ec09abda4b352691b37565cb48
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615139"
---
# <a name="skiasharp-lines-and-paths"></a>Строки и пути SkiaSharp

_Рисование линий и графики пути с помощью SkiaSharp_

[Выше](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) продемонстрировал, SkiaSharp `SKCanvas` класс содержит несколько методов для рисования прямоугольники, эллипсы и прямоугольники с закругленными углами. Этот раздел и последующих разделах рассматриваются различные классы, подключенной к Создание и Подготовка к просмотру *контуры*.

Графический контур наиболее общий подход для рисования линий и кривых в SkiaSharp. В этом разделе описывается использование `SKPath` объектов для рисования прямых линий, а также использовать коллекцию tiny прямых линий (вызывается *ломаной линии*) для рисования кривых, которые вы можете определить математически. Ниже будет обсуждаются различные виды кривых, поддерживаемых `SKPath`.

Все примеры программ, в этом разделе отображаются под заголовком **строки и пути** на домашней странице [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) программы, а затем в [ **Пути** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths) папку решения.

## <a name="lines-and-stroke-capslinesmd"></a>[Линии и концы штрихов](lines.md)

Сведения об использовании SkiaSharp для рисования линий с ограничениями различных stroke.

## <a name="path-basicspathsmd"></a>[Основные сведения о пути](paths.md)

Изучите SkiaSharp SKPath объект для объединения линий и кривых.

## <a name="the-path-fill-typesfill-typesmd"></a>[Типы заполнения пути](fill-types.md)

Обнаружение различных эффектов, поддерживаемых в средстве типы заполнения пути SkiaSharp.

## <a name="polylines-and-parametric-equationspolylinesmd"></a>[Ломаные линии и параметрические уравнения](polylines.md)

Использование SkiaSharp для отрисовки любой строки, которые можно определить с помощью параметрические уравнения.

## <a name="dots-and-dashesdotsmd"></a>[Точки и тире](dots.md)

Освойте тонкости механизмов рисования в SkiaSharp пунктирная и пунктирных линий.

## <a name="finger-paintingfinger-paintmd"></a>[Рисование пальцами](finger-paint.md)

Используйте пальцы для рисования на холсте.


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
