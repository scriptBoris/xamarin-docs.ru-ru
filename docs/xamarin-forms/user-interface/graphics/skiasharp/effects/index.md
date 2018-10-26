---
title: Эффекты SkiaSharp
description: Узнайте, как изменить отображение обычного графики с градиентом, мозаичное заполнение для точечных рисунков, режимы смешения, размытия и другие эффекты.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B3E06572-8E2A-49FA-90D1-444C394CD516
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
ms.openlocfilehash: 768fd0e1cd5ed08f42310d9aaf5993de7ec32e6b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131872"
---
# <a name="skiasharp-effects"></a>Эффекты SkiaSharp

SkiaSharp [ `SKPaint` ](xref:SkiaSharp.SKPaint) класс определяет шесть свойств, которые можно классифицировать в разделе термин из _эффекты_. Это свойства, изменяющие обычный отображения графики каким-либо образом. Эффекты SkiaSharp делятся на шесть категорий:

## <a name="path-effectscurveseffectsmd"></a>[Эффекты пути](../curves/effects.md)

Задайте [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) свойство `SKPaint` для объекта типа [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) пунктирные линии, а также обводки или Заливка области с шаблоном, созданным из путей. Ранее в этой серии, в этой статье изложено эффект пути [ **эффекты пути в SkiaSharp**](../curves/effects.md).

## <a name="shadersshadersindexmd"></a>[Шейдеры](shaders/index.md)

Задайте [ `Shader` ](xref:SkiaSharp.SKPaint.Shader) свойство `SKPaint` для объекта типа [ `SKShader` ](xref:SkiaSharp.SKShader) для отображения линейные и циклическая градиенты, мозаичный точечных рисунков и шаблонов шума Перлина.

## <a name="blend-modesblend-modesindexmd"></a>[Режимы смешения](blend-modes/index.md)

Задайте [ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode) свойство `SKPaint` члену [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode) перечисления для управления действиями при отображении рисунка источника в место назначения. SkiaSharp поддерживает все CSS композиции и blend режимы, включая режимы Duff Портер, режимы отделяемых наложения и режимы нелинейно разделяемых наложения.

## <a name="mask-filtersmask-filtersmd"></a>[Маска фильтры](mask-filters.md)

Задайте [ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter) свойство `SKPaint` для объекта типа [ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter) для размытия и другие эффекты альфа-канала.

## <a name="image-filtersimage-filtersmd"></a>[Фильтры образа](image-filters.md)

Задайте [ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter) свойство `SKPaint` для объекта типа [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) Размытие растровых изображений и Создание тени, тиснение, или Утопленный текст; эффекты.

## <a name="color-filterscolor-filtersmd"></a>[Цвет фильтры](color-filters.md)

Задайте [ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter) свойство `SKPaint` для объекта типа [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) для изменения цвета с помощью таблицы или матрицы преобразования.

Все в образце кода для эти статьи находятся в [ **SkiaSharpFormsDemos**](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/). На домашней странице выберите **SkiaSharp эффекты**.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
