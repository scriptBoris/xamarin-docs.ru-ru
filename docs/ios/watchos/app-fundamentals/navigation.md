---
title: Работа с watchOS навигации в Xamarin
description: В этом документе описывается работа с навигацией в приложении watchOS. В нем описывается модальное интерфейсы, иерархической навигации и интерфейсы на основе страниц.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 71978bebcdf6033a766ae2bcb75ae061ed215a8b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102377"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Работа с watchOS навигации в Xamarin

Самый простой навигации вариант доступен в watch — это простой [модального всплывающего окна](#modal) , отображается поверх текущей сцены.

Для приложения watch несколькими сцены существует две парадигмы навигации доступны:

- [Иерархическая навигация](#Hierarchical_Navigation)
- [При использовании интерфейсов](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>Модальное интерфейсы

Используйте `PresentController` метод, чтобы открыть контроллера интерфейса в модальном режиме. Контроллер интерфейса должна быть уже определена в **Interface.storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Контроллеры, представленные как модальная использовать весь экран (описывающих предыдущих сцены). По умолчанию присваивается заголовок **отменить** и его приводит к отмене использования контроллера.

Чтобы программное закрытие контроллера как модальная представленные, вызовите `DismissController`.

```csharp
DismissController();
```

Модальное экранов можно использовать структуру на основе страницы или в одной сценой.

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>Иерархическая навигация

Представляет сцены как стек, который может быть перехода назад, аналогично тому, как `UINavigationController` работает в iOS. Сцены можно помещается в стек навигации и выводятся из (программно или по выбору пользователя).

![](navigation-images/hierarchy-1.png "Сцены может быть помещается в стек переходов") ![](navigation-images/hierarchy-2.png "сцены может извлекаться из стека навигации")

Как и в iOS, left-edge-жеста перетаскивания переходит обратно в родительский контроллер в стеке иерархической навигации.

Оба [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog) и [WatchTables](https://developer.xamarin.com/samples/WatchTables) примеры содержат иерархической навигации.

### <a name="pushing-and-popping-in-code"></a>Перемещая и выталкивая в коде

Посмотрите, пакет не требует чрезмерно сбой «контроллер навигации» создаваться как iOS - попросту образоваться контроллера, используя `PushController` метода в стек навигации будут автоматически созданы и.

```csharp
PushController("secondPageController","some context info");
```

Экран Контрольные значения будет включать **обратно** кнопка в верхнем левом углу, но можно также программно удалить сцены из стека навигации с помощью `PopController`.

```csharp
PopController();
```

Так как с iOS, он также можно вернуть в корень стека навигации с помощью `PopToRootController`.

```csharp
PopToRootController();
```

### <a name="using-segues"></a>С помощью использования объекта Segue

Однако они плохо могут создаваться между сценами в раскадровке для определения иерархической навигации. Чтобы получить контекст для целевого сцены, вызовов операционной системы `GetContextForSegue` для инициализации нового контроллера интерфейса.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```
<a name="Page-Based_Interfaces"/>

## <a name="page-based-interfaces"></a>При использовании интерфейсов

При использовании интерфейсов считайте слева направо, аналогично тому, как `UIPageViewController` работает в iOS. Индикатор точки отображаются в нижней части экрана, чтобы показать, какие страницы отображается в данный момент.

![](navigation-images/paged-1.png "Первая страница образец") ![](navigation-images/paged-2.png "образец второй страницы") ![](navigation-images/paged-5.png "пятой странице образца")


Чтобы сделать интерфейс на основе страницы основной пользовательский Интерфейс для приложения watch, используйте `ReloadRootControllers` с массивом контроллеры интерфейса и контекстов:

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

Вы также можете отправить основанная на контроллер, который не является корнем с помощью `PresentController` из одного из других сцен в приложении.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>Связанные ссылки

- [WatchKitCatalog (пример)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [WatchTables (пример)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchTables/)
