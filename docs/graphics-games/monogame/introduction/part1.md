---
title: Часть 1 — Создание MonoGame кроссплатформенный
description: В этом пошаговом руководстве показано, как создать новый проект для iOS и Android с помощью MonoGame. Результатом является Visual Studio для Mac решения проект общего кода для нескольких платформ, а также один проект для каждой платформы. Этот проект будет отображать пустой синий экран при выполнении.
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: bd7990b94e678c205f9ce636f4eb0d28180fc6ec
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921993"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>Часть 1 — Создание MonoGame кроссплатформенный

_В этом пошаговом руководстве показано, как создать новый проект для iOS и Android с помощью MonoGame. Результатом является Visual Studio для Mac решения проект общего кода для нескольких платформ, а также один проект для каждой платформы. Этот проект будет отображать пустой синий экран при выполнении._

MonoGame позволяет разработки кросс платформенных игр с большей части повторное использование кода. В этом пошаговом руководстве основное внимание уделяется настройке решение, содержащее проекты для iOS и Android, а также проекте с общим кодом для кросс платформенного кода.

Закончив, мы иметь проект, который имеет правильную структуру для выполнения логики обновления игры и игры, рисование логику в 30 кадров в секунду. Он может использоваться в качестве базового проекта для любого MonoGame проекта. При выполнении, наш проект будет выглядеть следующим образом:

![Пустой синий экран](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio-for-mac"></a>Добавление MonoGame в Visual Studio для Mac

MonoGame можно добавлять в качестве надстройки в Visual Studio для Mac. На Mac, выберите **Visual Studio для Mac** > **Диспетчер надстроек...**  . В Windows выберите ** средства ** > **Диспетчер надстроек...**  . Выберите **коллекции** , раскройте **разработки игры** категорию и выберите **MonoGame Addin**, нажмите кнопку **установить**:

![Visual Studio для расширения галереи Mac, выбрав MonoGame](part1-images/image2.png)

> [!IMPORTANT]
> **Примечание**: Если **разработки игры** раздел не отображается в диспетчере надстройки, можно вручную загрузить и установить последнюю версию отсюда: http://www.monogame.net/downloads/. Необходимо перезапустить Visual Studio для Mac для шаблонов для отображения.

После установки MonoGame шаблоны будут отображаться в Visual Studio для Mac, как будет показано в следующем разделе.

## <a name="creating-a-new-solution"></a>Создание нового решения

В Visual Studio для Mac select **файл > новое решение**. В **новый проект** диалоговое окно, нажмите кнопку **Разное**, перейдите к пункту **Общие** выберите ** приложение универсальной MonoGame Mobile ** и нажмите кнопку Далее.

![Создание приложения MonoGame диалоговое окно нового проекта](part1-images/image3.png)

Назовите проект WalkingGame и нажмите кнопку Создать.

![Выбрать имя и расположение диалоговое окно нового проекта](part1-images/image4.png)

Теперь наш проект будет выполняться так же, как любой другой iOS или Android проекта. Проект необходимо запускать отображение cornflower синий фон.

![Пустое приложение синий фон](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>Исправление ошибок компиляции Android

Текущая версия элемента MonoGame шаблонов включает несколько синтаксических ошибок в папке Android `Activity1.cs` файл. Чтобы устранить эти проблемы, замените `OnCreate` функция со следующими:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```

## <a name="summary"></a>Сводка

В этом пошаговом руководстве рассматривается создание кросс платформенным проектом MonoGame, с помощью Visual Studio для Mac. Таким образом пустой синий экран. Этот проект может использоваться в качестве отправной точки для любой iOS и Android игры.

## <a name="related-links"></a>Связанные ссылки

- [MonoGame Android NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Документация по MonoGame API](http://www.monogame.net/documentation/?page=main)
