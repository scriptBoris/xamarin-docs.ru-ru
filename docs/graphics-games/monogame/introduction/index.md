---
title: Общие сведения о разработке игр с MonoGame
description: Этот составной пошаговом руководстве показано, как создать простое приложение 2D, с помощью MonoGame.  Она охватывает игры общие принципы программирования, таких как рисунки, ввод, игры, сущности и физический.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 46cc3a7e3bb6c58e04626c9d2cc9437c16ba19f5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33920810"
---
# <a name="introduction-to-game-development-with-monogame"></a>Общие сведения о разработке игр с MonoGame

_Этот составной пошаговом руководстве показано, как создать простое приложение 2D, с помощью MonoGame.  Она охватывает игры общие принципы программирования, таких как рисунки, ввод, игры, сущности и физический._

В этой статье Описание технологии MonoGame API для создания кросс платформенных игр. Полный список платформ см. в разделе [MonoGame веб-сайт](http://www.monogame.net/). Этот учебник будет использовать C# образцы кода, несмотря на то, что MonoGame, также будет полностью функционален F #.

MonoGame является кросс платформенных, с аппаратным ускорением API, предоставляя графики, управление состоянием аудио, игры, ввод и конвейера содержимого для импорта ресурсов. В отличие от большинства игры ядер MonoGame не предоставляют и применить к любой структуре модели или проекта.  Это означает, что разработчики могут организовывать их код, как это необходимо, это также означает, что требуется большой объем кода установки при первом запуске нового проекта.

Первый раздел в этом пошаговом руководстве основное внимание уделяется настройке пустой проект. Последний раздел охватывает все наши логику игры и содержимого, наиболее которой будет различных платформах записи.

В конце данного пошагового руководства будет создан простой игры, когда игрок можно управлять анимированных символом сенсорный ввод.  Несмотря на то, что это не технической точки зрения полный игры (поскольку у них нет этом условия), он демонстрирует многочисленные понятия разработки игр и может использоваться как основа для многих типов игры. 

Ниже показан результат выполнения данного пошагового руководства:

![Анимация пример игры символ указателя мыши](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame и XNA

Библиотека MonoGame предназначен для имитации библиотеки Microsoft XNA в синтаксис и функции.  Все объекты MonoGame существует в пространстве имен Microsoft.Xna — что большая часть кода XNA для использования в MonoGame без изменения. 

Разработчики, знакомые с XNA уже будут знакомы с синтаксисом MonoGame и разработчиков, Дополнительные сведения о работе с MonoGame смогут обращаться к существующей документации XNA пошаговые руководства, документация по API и обсуждения.


## <a name="walkthrough-parts"></a>Части пошагового руководства

- [Часть 1 – Создание проекта MonoGame кроссплатформенный](~/graphics-games/monogame/introduction/part1.md)
- [Часть 2 — реализация WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Связанные ссылки

- [Проект MonoGame WalkingGame (пример)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [XNB шрифты iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB шрифты Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [MonoGame Android в NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame операций ввода-вывода в NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Документация по MonoGame API](http://www.monogame.net/documentation/?page=main)
