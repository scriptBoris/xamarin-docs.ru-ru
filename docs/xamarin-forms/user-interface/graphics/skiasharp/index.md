---
title: Использование SkiaSharp в Xamarin.Forms
description: SkiaSharp — это система Двухмерная графика для .NET и C# на платформе Skia графики открытым исходным кодом, которая широко используется в продуктах Google. В этом руководстве описывается использование SkiaSharp для двумерной графики в приложениях Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
author: charlespetzold
ms.author: chape
ms.date: 09/11/2017
ms.openlocfilehash: f7d97b798bf2a5a75af0731a665fe212491a6516
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615877"
---
# <a name="using-skiasharp-in-xamarinforms"></a>Использование SkiaSharp в Xamarin.Forms

_Использование SkiaSharp для двумерной графики в приложениях Xamarin.Forms_

SkiaSharp — это система Двухмерная графика для .NET и C# на платформе Skia графики открытым исходным кодом, которая широко используется в продуктах Google. SkiaSharp в приложениях Xamarin.Forms можно использовать для рисования двухмерной векторной графики, точечные рисунки и текст. См. в разделе [двухмерного рисования](~/graphics-games/skiasharp/index.md) руководство более общие сведения о библиотеке SkiaSharp и других учебников.

В этом руководстве предполагается, что вы знакомы с программированием для Xamarin.Forms.

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Веб-семинар: SkiaSharp для Xamarin.Forms**

## <a name="skiasharp-preliminaries"></a>Предварительные действия SkiaSharp

SkiaSharp для Xamarin.Forms упакован в виде пакета NuGet. После создания решения Xamarin.Forms в Visual Studio или Visual Studio для Mac, можно использовать диспетчер пакетов NuGet для поиска **SkiaSharp.Views.Forms** пакета и добавить его в решение. Если проверить **ссылки** раздел проекта после добавления SkiaSharp, можно увидеть, что различные **SkiaSharp** библиотеки были добавлены все проекты в решении.

Если приложение Xamarin.Forms предназначено для iOS, используйте страницы свойств проекта для изменения минимальной цели развертывания iOS до версии 8.0.

На любой странице C#, использующий SkiaSharp нужно будет включать `using` директив для [ `SkiaSharp` ](https://developer.xamarin.com/api/namespace/SkiaSharp/) пространство имен, которое включает в себя все SkiaSharp классы, структуры и перечисления, которые будут использоваться в графики программирование. Также стоит `using` директив для [ `SkiaSharp.Views.Forms` ](https://developer.xamarin.com/api/namespace/SkiaSharp.Views.Forms/) пространство имен для классов, определенных в Xamarin.Forms. Это гораздо меньше пространства имен, с наиболее важных класса, [ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/). Этот класс является производным от Xamarin.Forms `View` класса и размещает в SkiaSharp графический вывод данных.

> [!IMPORTANT]
> `SkiaSharp.Views.Forms` Пространство имен также содержит `SKGLView` класс, производный от `View` , но использует OpenGL для рендеринга графики. Для простоты в этом руководстве задействует для `SKCanvasView`, однако применение `SKGLView` вместо очень похож.

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[Основы рисования в SkiaSharp](basics/index.md)

Самый простой числа графики, которые можно нарисовать с SkiaSharp представлен ряд круги, овалы и прямоугольники. При отображении эти цифры, вы узнаете о координатах SkiaSharp, размеры и цвета.

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[Строки и пути SkiaSharp](paths/index.md)

Графический путь представляет собой ряд соединенных прямых линий и кривых. Пути можно заштриховывать, заполнен, или оба. В этом разделе включает в себя многие аспекты рисования линии, включая stroke концами и соединениями, а также пунктирная и пунктирные линии, но не хватает, кривая геометрических объектов.

## <a name="skiasharp-transformstransformsindexmd"></a>[Преобразование SkiaSharp](transforms/index.md)

Преобразования также позволяют графические объекты равномерно перевода, масштабировать, поворачивать, и синхронизована. В этой статье также показано, как можно использовать матрицу стандартные преобразования 3 x 3 для создания неаффинные преобразования и применение преобразования к пути.

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[Пути и кривые SkiaSharp](curves/index.md)

Просмотр путей продолжается с добавлением кривых в контурные объекты и использовать другие функции эффективные пути. Вы увидите, как можно указать весь путь в краткую текстовую строку, как использовать эффекты пути и как внутренние компоненты пути во всех подробностях.

## <a name="skiasharp-bitmapsbitmapsindexmd"></a>[Растровые изображения SkiaSharp](bitmaps/index.md)

Точечные рисунки имеют прямоугольную массивы биты, соответствующие точкам устройства отображения. Этой серии статей показано, как загрузить, сохранить, отображения, создать, рисовать на, анимация и доступ к биты SkiaSharp точечных рисунков.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [SkiaSharp с веб-семинар Xamarin.Forms (видео)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
