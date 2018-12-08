---
title: Преобразование Skiasharp
description: В этой статье рассматриваются преобразования для отображения графики SkiaSharp в приложениях Xamarin.Forms и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: a57e50f098c92dbfcdcaa3139565d2ba0e291e3d
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057539"
---
# <a name="skiasharp-transforms"></a>Преобразование Skiasharp

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Дополнительные сведения о преобразованиях для отображения графики SkiaSharp_

SkiaSharp поддерживает традиционному графическому преобразований, которые реализуются как методы класса [ `SKCanvas` ](xref:SkiaSharp.SKCanvas) объекта. Математически, alter преобразования координаты и размеры, указанные в `SKCanvas` Рисование функции, как подготавливаются к просмотру графических объектов. Преобразования часто являются удобным для повторяющихся графики или анимации. Некоторые методы &mdash; недоступны растровые изображения или текста &mdash; невозможны без использования преобразований.

Преобразование skiasharp поддерживают следующие операции:

- *Перевод* для сдвига координаты из одного расположения в другое
- *Масштаб* для увеличения или уменьшения координаты и размеры
- *Поворот* для поворота координаты вокруг точки
- *Наклон* для сдвига координаты по горизонтали или вертикали, чтобы прямоугольника становится параллелограмм

Они известны как *аффинные* преобразования. Аффинные преобразования всегда сохраняет параллельные линии и никогда не вызывают координат или размер станет бесконечной. Квадрат никогда не преобразуются в строки, отличное от параллелограмм, а окружность никогда не преобразуется в ничего, кроме эллипса.

SkiaSharp также поддерживает неаффинные преобразования (также называется *проектное* или *перспективы* преобразует) на основе на матрицу стандартные преобразования 3 x 3. Неаффинных преобразование дает квадрат, чтобы преобразовать в любой выпуклая четырехугольника осуществляется плавный переход, являющийся четырехсторонняя фигуры со всех внутренних углов менее чем на 180 градусов. Non аффинные преобразования может вызвать координаты или размеры стать бесконечный, но они очень важны для объемных эффектов.

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>Различия между SkiaSharp и преобразованиями, Xamarin.Forms

Xamarin.Forms также поддерживает преобразований, которые аналогичны шагам в SkiaSharp. Xamarin.Forms [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) класс определяет следующие свойства преобразования:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) и [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation), [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX), и [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

`RotationX` И `RotationY` свойства являются точки зрения преобразований, которые создают квази трехмерные эффекты.

Существует несколько важных различий между преобразование skiasharp и Xamarin.Forms преобразований:

Первое отличие в том, что преобразование skiasharp применяются ко всему `SKCanvas` во время преобразования Xamarin.Forms применяются для отдельных `VisualElement` производные от него. (Можно применить преобразование Xamarin.Forms, которое требуется `SKCanvasView` объекта, так как `SKCanvasView` является производным от `VisualElement`, но внутри этого `SKCanvasView`, применить преобразования к SkiaSkarp.)

Преобразование skiasharp, относительно верхнего левого угла `SKCanvas` во время преобразования Xamarin.Forms являются относительно верхнего левого угла `VisualElement` к которому они применяются. Это различие очень важно при применении масштабирования и поворота преобразования, так как эти преобразования всегда относительны состоянию на определенный момент.

Действительно разница заключается в том, что преобразование skiasharp *методы* во время преобразования Xamarin.Forms *свойства*. Это семантического различия за пределы различии: преобразование skiasharp операции во время преобразования Xamarin.Forms задать состояние. Преобразование skiasharp применяются к объектам впоследствии графических элементов, но не на графические объекты, которые рисуются перед применением преобразования. Напротив Xamarin.Forms преобразование применяется к ранее отображаемого элемента, как только свойство имеет значение. Преобразование skiasharp являются накопительными, так как эти методы вызываются; Xamarin.Forms преобразований заменяются в том случае, когда свойству с другим значением.

Все примеры программ, в этом разделе отображаются в **преобразует SkiaSharp** раздел [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) программы. Исходный код можно найти в [ **преобразует** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms) папку решения.

## <a name="the-translate-transformtranslatemd"></a>[Преобразование переноса](translate.md)

Сведения об использовании преобразования переноса необходимо сдвинуть SkiaSharp графики.

## <a name="the-scale-transformscalemd"></a>[Преобразование масштаба](scale.md)

Обнаружение преобразование SkiaSharp масштабирования для масштабирования объектов для различных размеров.

## <a name="the-rotate-transformrotatemd"></a>[Преобразование циклического сдвига](rotate.md)

Изучите эффекты и анимации в средстве SkiaSharp преобразование циклического сдвига.

## <a name="the-skew-transformskewmd"></a>[Преобразование наклона](skew.md)

См. в разделе, как создать преобразование наклона мозаичный графического объекта.

## <a name="matrix-transformsmatrixmd"></a>[Преобразования матрицы](matrix.md)

Погрузитесь глубже в преобразование skiasharp с матрица универсальный преобразования.

## <a name="touch-manipulationstouchmd"></a>[Манипуляции сенсорного ввода](touch.md)

Используйте матрицы преобразования для реализации манипуляции сенсорного ввода для перетаскивания, масштабирования и поворота.

## <a name="non-affine-transformsnon-affinemd"></a>[Неаффинные преобразования](non-affine.md)

Не ограничивайтесь oridinary с эффектами неаффинных преобразования.

## <a name="3d-rotation3d-rotationmd"></a>[Трехмерный поворот](3d-rotation.md)

Используйте неаффинные преобразования для поворота 2D объектов в трехмерном пространстве.


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
