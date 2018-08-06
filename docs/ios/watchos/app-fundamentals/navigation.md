---
title: Работа с watchOS навигации в Xamarin
description: В этом документе описывается работа с навигацией в приложении watchOS. Он описывает, модальные интерфейсы, иерархическая навигация и при использовании интерфейсов.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: c9bcfc388164060549ca7010d11671abfa8230ac
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790644"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Работа с watchOS навигации в Xamarin

Самый простой навигации вариант доступен в watch — это простой [модальное окно](#modal) , отображается поверх текущей сцены.

Для наблюдения за несколькими сцены там приложений будут доступны две парадигмы навигации:

- [Иерархическая навигация](#Hierarchical_Navigation)
- [При использовании интерфейсов](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>Модальные интерфейсов

Используйте `PresentController` метод, чтобы открыть интерфейс контроллера как модальная. Контроллер интерфейса уже должен быть определен в **Interface.storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Как модальная представленный контроллеры используют весь экран (с охватом предыдущих сцены). По умолчанию имеет значение заголовка **отменить** и коснувшись его закроет контроллера.

Чтобы программное закрытие контроллера как модальная представленный, вызовите `DismissController`.

```csharp
DismissController();
```

Модальные экраны может быть использование макета на странице или в одной сцены.

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>Иерархическая навигация

Представляет сцены как стек, который может быть навигацию обратно через, аналогично тому, как `UINavigationController` работает в iOS. Сцены можно помещается в стек переходов и выводятся из (программным путем или путем выбора пользователя).

![](navigation-images/hierarchy-1.png "Сцены может быть помещается в стек переходов") ![](navigation-images/hierarchy-2.png "сцены может извлекаться из стека навигации")

Как и для операций ввода-вывода, left проведите edge переходит обратно в родительский контроллер в стеке иерархической навигации.

Оба [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog) и [WatchTables](https://developer.xamarin.com/samples/WatchTables) примеры иерархической навигации.

### <a name="pushing-and-popping-in-code"></a>Помещают и извлекают в коде

Посмотрите пакета не требуется чрезмерно сбой «controller навигации» создаваться как iOS - просто поместите контроллера с помощью `PushController` метод и стек навигации будет автоматически создана.

```csharp
PushController("secondPageController","some context info");
```

Экран Контрольное значение будет содержать **обратно** кнопка в верхнем левом углу, но можно также программно удалить сцены из стека навигации с помощью `PopController`.

```csharp
PopController();
```

Так как с iOS, также для возврата в корневой каталог в стеке навигации с помощью `PopToRootController`.

```csharp
PopToRootController();
```

### <a name="using-segues"></a>С помощью Segues

Segues могут быть созданы между сцен в раскадровку определение иерархической навигации. Для получения контекста для целевой сцены, операционная система вызывает `GetContextForSegue` для инициализации нового интерфейса контроллера.

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

При использовании интерфейсов проведите слева направо, аналогично тому, как `UIPageViewController` работает в iOS. Индикатор точки отображаются в нижней части экрана, чтобы отобразить страницу в текущий момент отображается.

![](navigation-images/paged-1.png "Первая страница образец") ![](navigation-images/paged-2.png "образец второй страницы") ![](navigation-images/paged-5.png "пятой странице образца")


Чтобы сделать интерфейс на основе страницы основной пользовательский Интерфейс для наблюдения за приложения, используйте `ReloadRootControllers` с помощью массива контроллеров интерфейс и контекстов:

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

Также можно представить на странице контроллера, который не является корневым с помощью `PresentController` из одного из других сцен в приложении.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>Связанные ссылки

- [WatchKitCatalog (пример)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [WatchTables (пример)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchTables/)
