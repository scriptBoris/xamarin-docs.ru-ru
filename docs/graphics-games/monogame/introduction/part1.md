---
title: Часть 1 — Создание кроссплатформенного MonoGame
description: В этом пошаговом руководстве демонстрируется создание нового проекта для iOS и Android с помощью MonoGame. Результат — Visual Studio для Mac решение с помощью проекта общего кода кроссплатформенных приложений, а также один проект для каждой платформы. Этот проект будет отображаться пустой синего экрана при выполнении.
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 82b1408cafedf98a8619e8e039ba00b332f74516
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "33921993"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>Часть 1 — Создание кроссплатформенного MonoGame

_В этом пошаговом руководстве демонстрируется создание нового проекта для iOS и Android с помощью MonoGame. Результат — Visual Studio для Mac решение с помощью проекта общего кода кроссплатформенных приложений, а также один проект для каждой платформы. Этот проект будет отображаться пустой синего экрана при выполнении._

MonoGame позволяет разрабатывать кросс платформенных игр с крупным сегментом повторного использования кода. В этом пошаговом руководстве основное внимание уделяется настройке решение, содержащее проекты для iOS и Android, а также проект общего кода для кросс платформенного кода.

Когда все готово, мы будем проекта, содержащего нужную структуру для выполнения логики обновления игры и игр Рисование логики 30 кадров в секунду. Он может использоваться в качестве базового проекта для любого проекта MonoGame. При выполнении, наш проект будет выглядеть следующим образом:

![Пустой синий экран](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio-for-mac"></a>Добавление MonoGame Visual Studio для Mac

MonoGame можно добавить как надстройку Visual Studio для Mac. На компьютере Mac, выберите **Visual Studio для Mac** > **Диспетчер надстроек...**  . В Windows, выберите ** средства ** > **Диспетчер надстроек...**  . Выберите **коллекции** вкладке, разверните узел **Разработка игр** категорию и выберите **MonoGame Addin**, затем нажмите кнопку **установить**:

![Visual Studio для Mac коллекции расширений, выбрав MonoGame](part1-images/image2.png)

> [!IMPORTANT]
> **Примечание**: Если **Разработка игр** раздел не отображается в диспетчере надстройки, можно вручную загрузить и установить последнюю версию отсюда: http://www.monogame.net/downloads/. Может потребоваться перезапустить Visual Studio для Mac для шаблонов для отображения.

После установки MonoGame шаблоны отображались в Visual Studio для Mac, как мы увидим в следующем разделе.

## <a name="creating-a-new-solution"></a>Создание решения

В Visual Studio для Mac выберите **файл > новое решение**. В **новый проект** диалоговое окно, щелкните **Разное**, прокрутите до **Общие** выберите ** универсальной MonoGame мобильного приложения ** и нажмите кнопку Далее.

![Создание приложения MonoGame диалоговое окно нового проекта](part1-images/image3.png)

Назовите проект WalkingGame и нажмите кнопку Создать:

![Выбрав имя и расположение диалоговое окно нового проекта](part1-images/image4.png)

Теперь наш проект будет выполняться так же, как любой другой iOS или Android проекта. Проект следует запускать отображение cornflower синий фон:

![Пустое приложение синий фон](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>Устранение ошибок компиляции Android

В текущей версии шаблонов MonoGame представлено несколько синтаксических ошибок в Android `Activity1.cs` файл. Чтобы устранить эти проблемы, замените `OnCreate` функцию со следующими:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```

## <a name="summary"></a>Сводка

В этом пошаговом руководстве рассматривалось создание кросс платформенных проекта MonoGame, с помощью Visual Studio для Mac. Результатом этого является пустой синий экран. Этот проект можно использовать в качестве отправной точки для любой iOS и Android игры.

## <a name="related-links"></a>Связанные ссылки

- [NuGet MonoGame Android](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Документация по API MonoGame](http://www.monogame.net/documentation/?page=main)
