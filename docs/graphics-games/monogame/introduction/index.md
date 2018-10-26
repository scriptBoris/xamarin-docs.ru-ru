---
title: Введение в разработку игр с помощью MonoGame
description: Этот составной пошаговом руководстве показано, как создать простое приложение 2D, с помощью MonoGame.  Здесь рассматриваются распространенные игры принципы программирования, таких как графики, ввод, игр, сущности и физики.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 4ab98d59bc74672f9531f4dbd3c33a6270582612
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "33920810"
---
# <a name="introduction-to-game-development-with-monogame"></a>Введение в разработку игр с помощью MonoGame

_Этот составной пошаговом руководстве показано, как создать простое приложение 2D, с помощью MonoGame.  Здесь рассматриваются распространенные игры принципы программирования, таких как графики, ввод, игр, сущности и физики._

В этой статье описывается технология MonoGame API для создания кросс платформенных игр. Полный список платформ, см. в разделе [MonoGame веб-сайт](http://www.monogame.net/). Этом руководстве будет использоваться C# примеры кода, несмотря на то, что MonoGame полностью функционален F# также.

MonoGame является кросс платформенной, аппаратным ускорением API, который обеспечивает графики, управление состоянием аудио, игр, входных данных и конвейера содержимого для импорта ресурсов. В отличие от большинства игровых движков MonoGame не предоставляют и не накладывают любую структуру шаблона или проекта.  Это означает, что разработчики могут организовывать код, как это необходимо, это также означает, что требуется немного кода установки при первом запуске проекта.

В первой части этого пошагового руководства посвящена настройке пустой проект. Последний раздел охватывает сопутствующего написания всех наших логику игры и материалы по наиболее которой будет различных платформах.

В конце этого пошагового руководства будет создан простую игру, где игрок можно управлять анимированных символом сенсорный ввод.  Несмотря на то, что это не является технически полной версии игры (так как у него нет или проиграли условий), он демонстрирует многочисленные понятия разработки игр и может использоваться в качестве основы для многих типов игры. 

Ниже показан результат выполнения этого пошагового руководства:

![Анимация пример игры символ после мыши](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame и XNA

В библиотеке MonoGame предназначен для имитации библиотеки Microsoft XNA в синтаксисе и функциональные возможности.  Все объекты MonoGame существует в пространстве имен Microsoft.Xna – что большая часть кода XNA для использования в MonoGame без изменения. 

Разработчики, знакомые с XNA уже будут знакомы с синтаксисом MonoGame и разработчиков, Дополнительные сведения о работе с MonoGame будут иметь возможность ссылаться на существующие пошаговые руководства online XNA, документация по API и обсуждения.


## <a name="walkthrough-parts"></a>Части пошагового руководства

- [Часть 1 — Создание кроссплатформенного проекта MonoGame](~/graphics-games/monogame/introduction/part1.md)
- [Часть 2 – реализация WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Связанные ссылки

- [Проекта MonoGame WalkingGame (пример)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [Шрифты XNB iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [Android XNB шрифты](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [MonoGame Android в NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS в NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Документация по API MonoGame](http://www.monogame.net/documentation/?page=main)
