---
title: 2D игровое ядро CocosSharp
description: Этот документ содержит ссылки на различные ресурсы, посвященные разработке игр с помощью CocosSharp. Связанное содержимое описывает примеры приложений, рисование, анимация и многое другое.
ms.prod: xamarin
ms.assetid: 5E72869D-3541-408B-AB64-D34C777AFB79
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: add73360ea98d8c516e413f0cc0264f68c58d79d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107018"
---
# <a name="cocossharp-2d-game-engine"></a>2D игровое ядро CocosSharp

_CocosSharp — это библиотека для создания 2D-игр с помощью C# и F#. Это ядро популярных Cocos2D перенос на .NET._

## <a name="introduction-to-cocossharp"></a>Введение в CocosSharp

2D игровое ядро CocosSharp предоставляет технологию для создания кросс платформенных игр. Полный список поддерживаемых платформ см. в разделе [CocosSharp вики-сайта на сайте GitHub](https://github.com/mono/CocosSharp/wiki).
Используйте эти руководства C# примеры кода, несмотря на то, что CocosSharp полностью функционален F# также.

Предоставляемые ядром CocosSharp [MonoGame framework](http://www.monogame.net/), который сам является кросс платформенной, аппаратным ускорением API, который обеспечивает графики, управление состоянием аудио, игр, входных данных и конвейера содержимого для импорта ресурсов.
CocosSharp представляет собой уровень эффективную абстракцию, хорошо подходят для 2D-игр.
Кроме того больше игры выполнить собственные оптимизацию за пределами их основных библиотек игры при увеличении сложности. Другими словами CocosSharp предоставляет сочетание простота использования и производительности, позволяя разработчикам быстро начать работу без ограничения игр размера и сложности.

Это практических видео демонстрируется создание простой CocosSharp кросс платформенных игр:

> [!Video https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Developing-Cross-platform-2D-Games-in-C-and-CocosSharp/player]

## <a name="bouncinggamegraphics-gamescocossharpbouncing-gamemd"></a>[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)

![BouncingGame](images/bouncing-game.png "BouncingGame")

В этом руководстве описывается BouncingGame, включая способы работы с игр, различных визуальных элементов, которые используются для создания игр, добавление логику игры и многое другое.

## <a name="fruity-falls-gamegraphics-gamescocossharpfruity-fallsmd"></a>[Игре Fruity Falls](~/graphics-games/cocossharp/fruity-falls.md)

![Снимок экрана игре Fruity Falls](images/fruity-falls.png "экрана игре Fruity Falls")

В этом руководстве описывается игре Fruity падает, охватывающие распространенных CocosSharp и понятия разработки игр, например физики, управления содержимым, состояния игры и проектирование игр.  

## <a name="coin-time-gamegraphics-gamescocossharpcointimemd"></a>[Игра время монеты](~/graphics-games/cocossharp/cointime.md)

![Coin снимок экрана игры время](images/cointime.png "снимок экрана монеты время для игр")

Время монеты — полный платформер, игры для iOS и Android. Цель игры — собрать все монеты уровня и, обратитесь к двери выхода избегая монстров и препятствия.

## <a name="drawing-geometry-with-ccdrawnodegraphics-gamescocossharpccdrawnodemd"></a>[Рисование геометрия с CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md)

![Фигуры с CCDrawNode](images/ccdrawnode.png "фигуры с CCDrawNode")

CCDrawNode предоставляет методы для рисования объектов-примитивов, таких как линий, кругов и треугольников.

## <a name="animating-with-ccactiongraphics-gamescocossharpccactionmd"></a>[Анимация с CCAction](~/graphics-games/cocossharp/ccaction.md)

![Анимированный значок CCAction](images/ccaction.png "анимация с CCAction")

`CCAction` является базовым классом, который может использоваться для анимации объектов CocosSharp. В этом руководстве описывается встроенная `CCAction` реализации для общих задач, таких как расположение, масштаба и вращение. Он также рассматривает способы создания пользовательских реализаций путем наследования от `CCAction`.

## <a name="using-tiled-with-cocossharpgraphics-gamescocossharptiledmd"></a>[Использование Tiled с CocosSharp](~/graphics-games/cocossharp/tiled.md)

![Уровень в игре](images/tiled.png "уровень в игре")

Мозаичная мощный, гибкий и сопоставляет отлаженное приложение для создания плитки самостоятельной и изометрической для игр. CocosSharp предоставляет встроенную интеграцию для копиями в собственном формате.

## <a name="entities-in-cocossharpgraphics-gamescocossharpentitiesmd"></a>[Сущности в CocosSharp](~/graphics-games/cocossharp/entities.md)

![Космического корабля, управляемого из игры](images/entities.png "космического корабля, управляемого из игры")

Шаблон сущности — это эффективное средство для организации кода игры. Это улучшает читаемость, благодаря этому код проще обслуживать и использует функции, встроенные родители потомки.

## <a name="handling-multiple-resolutions-in-cocossharpgraphics-gamescocossharpresolutionsmd"></a>[Обработка нескольких разрешений в CocosSharp](~/graphics-games/cocossharp/resolutions.md)

![Сетка, представляющий разрешение экрана](images/resolutions.png "сетки, представляющий разрешение экрана")

В этом руководстве показано, как работать с CocosSharp для разработки игр, которые правильного отображения на устройствах в различные способы их устранения.

## <a name="cocossharp-content-pipelinegraphics-gamescocossharpcontent-pipelineindexmd"></a>[Конвейер содержимого CocosSharp](~/graphics-games/cocossharp/content-pipeline/index.md)

![XNB](images/content-pipeline.png "XNB")

Конвейерами содержимого часто используются в разработке игр для оптимизации содержимого и отформатировать его таким образом, что его можно загрузить на определенных оборудования или с помощью определенных платформ разработки игр.

## <a name="improving-frame-rate-with-ccspritesheetgraphics-gamescocossharpccspritesheetmd"></a>[Повышение частоты кадров с помощью CCSpriteSheet](~/graphics-games/cocossharp/ccspritesheet.md)

![Дерево с помощью CCSpriteSheet](images/ccspritesheet.png "дерева с помощью CCSpriteSheet")

`CCSpriteSheet` предоставляет функциональные возможности для объединения и с помощью многих файлов изображений в одну текстуру. Уменьшая число текстуры повышает время загрузки игр и частоте кадров.

## <a name="texture-caching-using-cctexturecachegraphics-gamescocossharptexture-cachemd"></a>[Кэширование текстуры с помощью CCTextureCache](~/graphics-games/cocossharp/texture-cache.md)

![Это представление как CocosSharp кэширует образы](images/texture-cache.png "представлением как CocosSharp кэширует образов")

В CocosSharp `CCTextureCache` класс предоставляет стандартный способ упорядочить, кэширования и выгрузки содержимого. 

## <a name="2d-math-with-cocossharpgraphics-gamescocossharpmathmd"></a>[Двухмерные математические операции с CocosSharp](~/graphics-games/cocossharp/math.md)

![Поворот изображения](images/math.png "Поворот изображения")

В этом руководстве описывается 2D математики для разработки игр. Он использует CocosSharp демонстрирует, как выполнять типовые задачи разработки игр и объясняет математически этих задач.

## <a name="performance-and-visual-effects-with-ccrendertexturegraphics-gamescocossharpccrendertexturemd"></a>[Производительность и визуальные эффекты с CCRenderTexture](~/graphics-games/cocossharp/ccrendertexture.md)

![Спрайт из игры](images/ccrendertexture.png "спрайт из игры")

`CCRenderTexture` Класс предоставляет функциональные возможности для подготовки к просмотру нескольких объектов CocosSharp в одну текстуру. После создания `CCRenderTexture` экземпляров может использоваться для эффективной визуализации графики и реализации визуальные эффекты.
