---
title: Двухмерного рисования в SkiaSharp
description: В этом документе Обзор двумерные кросс платформенных рисования в SkiaSharp. Ссылки на различные руководства, описывающие SkiaSharp и его различные интерфейсы API.
ms.prod: xamarin
ms.techonology: xamarin-skiasharp
ms.assetid: A8A61421-4544-422A-A7E0-9355C67DF21E
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: 7207f33e56f566a5528d93f9957e2ff780a22a65
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615526"
---
# <a name="2d-drawing-with-skiasharp"></a>Двухмерного рисования в SkiaSharp

SkiaSharp предоставляет мощный API C# для выполнения двумерной графики. Он работает на платформе [библиотеки Google Skia](http://skia.org), той же библиотеке, обеспечивающий работу графического стеки Google Chrome, Firefox и Android.

[![](images/ide-sml.png "SkiaSharp предоставляет мощный API C# для выполнения двумерная графика")](images/ide.png#lightbox)

SkiaSharp является переносимой библиотеки и удобно поставляется как [кроссплатформенный пакет NuGet](https://www.nuget.org/packages/SkiaSharp)и поддерживает следующие платформы по умолчанию: macOS, Windows Desktop, Xamarin.iOS и Xamarin.Android.

## <a name="introduction-to-skiasharpgraphics-gamesskiasharpintroductionmd"></a>[Введение в SkiaSharp](~/graphics-games/skiasharp/introduction.md)

Обзор основных понятий SkiaSharp и пример кода для отрисовки графики, текст, растровые изображения и использовать фильтры образа.

## <a name="skiasharp-tutorials-for-xamarinformsxamarin-formsuser-interfacegraphicsskiasharpindexmd"></a>[SkiaSharp руководства по Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)

Узнайте, как для работы с кросс-графики платформы, которое отрисовывает в Xamarin.Forms:

- [Основы рисования](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)
  * [Рисование простого круга](~/xamarin-forms/user-interface/graphics/skiasharp/basics/circle.md)
  * [Интеграция с Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)
  * [Пиксели и аппаратно независимые единицы](~/xamarin-forms/user-interface/graphics/skiasharp/basics/pixels.md)
  * [Простая анимация](~/xamarin-forms/user-interface/graphics/skiasharp/basics/animation.md)
  * [Интеграция текста и графики](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)
  * [Основы растрового изображения](~/xamarin-forms/user-interface/graphics/skiasharp/basics/bitmaps.md)
- [Строки и пути](~/xamarin-forms/user-interface/graphics/skiasharp/paths/index.md)
  * [Линии и концы штрихов](~/xamarin-forms/user-interface/graphics/skiasharp/paths/lines.md)
  * [Основные сведения о пути](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)
  * [Типы заполнения пути](~/xamarin-forms/user-interface/graphics/skiasharp/paths/fill-types.md)
  * [Ломаные линии и параметрические уравнения](~/xamarin-forms/user-interface/graphics/skiasharp/paths/polylines.md)
  * [Точки и тире](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)
  * [Рисование пальцами](~/xamarin-forms/user-interface/graphics/skiasharp/paths/finger-paint.md)
- [Преобразования](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/index.md)
  * [Преобразование переноса](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)
  * [Преобразование масштаба](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/scale.md)
  * [Преобразование циклического сдвига](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)
  * [Преобразование наклона](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/skew.md)
  * [Преобразования матрицы](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md)
  * [Манипуляции сенсорного ввода](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)
  * [Non аффинные преобразования](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)
  * [Трехмерный поворот](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)
- [Пути и кривые](~/xamarin-forms/user-interface/graphics/skiasharp/curves/index.md)
  * [Три способа нарисовать дугу](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)
  * [Три типа кривых Безье](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)
  * [Данные пути SVG](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)
  * [Обрезка изображения по границам области с помощью путей](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md)
  * [Эффекты пути](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)
  * [Пути и текст](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)
  * [Сведения о пути и перечисление](~/xamarin-forms/user-interface/graphics/skiasharp/curves/information.md)
- [Растровые изображения](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/index.md)
  * [Вывод растровых изображений](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/displaying.md)
  * [Создание и рисование поверх растровых изображений](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/drawing.md)
  * [Обрезка растровых изображений](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/cropping.md)
  * [Сегментированный вывод растровых изображений](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/segmented.md)
  * [Сохранение растровых изображений в файлы](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/saving.md)
  * [Доступ к битам пикселей в растровых изображениях](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/pixel-bits.md)
  * [Анимация растровых изображений](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/animating.md)

## <a name="platform-specific-notesgraphics-gamesskiasharpplatformmd"></a>[Примечания для конкретных платформ](~/graphics-games/skiasharp/platform.md)

На этой странице описываются инструкции по настройке для SkiaSharp на разных платформах, включая iOS, Android, macOS и Windows.

## <a name="api-documentationhttpsdeveloperxamarincomapinamespaceskiasharp"></a>[Документация по API](https://developer.xamarin.com/api/namespace/SkiaSharp/)

Вы можете просмотреть [документации по API](https://developer.xamarin.com/api/namespace/SkiaSharp/) для SkiaSharp на нашем веб-сайте.

## <a name="work-in-progress"></a>Выполняемая работа

SkiaSharp находится на стадии, мы хотим поделиться информацией с нашего сообщества. Хотя мы привязку важные части Skia API, объем работы осталось выполнить. Мы используем стабильный C API, отображенные с Skia, и мы планируем заключается в дополнение к привязок C Skia раскрывается полностью к интерфейсам API при работе.

Чтобы помочь нам руководство по нашим усилиям привязки, оставьте комментарии и предложения как проблемы в репозитории GitHub [ http://github.com/mono/SkiaSharp ](http://github.com/mono/SkiaSharp).
