---
title: элемент управления меню (Force Touch) в Xamarin watchOS
description: В этом документе описывается, как использовать жест касания force watchOS в Xamarin. Он описывает, как реагировать на касание force, как добавлять меню и изменения элементов меню.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 7696c820ab6fdf19bdef46db31061fb5914e6cf4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109761"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>элемент управления меню (Force Touch) в Xamarin watchOS

Контрольные значения обеспечивает Force Touch жест, который активирует меню, при реализации на экране приложения watch.

![](menu-images/menu.png "Отображение меню Apple Watch")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Реагирование на Force Touch

Если `Menu` реализовано для контроллера интерфейса, когда пользователь выполняет Force Touch меню будет отображаться. Если меню не был реализован, кратко анимируется экрана никакие другие действия не выполняются.

Штрихи force не связаны с иной конкретный элемент на экране; только одно меню можно подключить к контроллеру интерфейса, и она будет отображаться независимо от того, где происходит press Force Touch на экране.

От одного до четырех меню могут быть представлены параметры.


## <a name="adding-a-menu"></a>Добавление меню

Объект `Menu` должны добавляться `InterfaceController` раскадровки во время разработки. При перетаскивании элемента управления меню на контроллера интерфейса нет никаких visual признаков того, на просмотр раскадровки, но **меню** отображается в **Структура документа** панели:

![](menu-images/menu-action.png "Изменение меню во время разработки")

До четырех меню можно добавить элементы управления меню. Они могут быть настроены в **свойства** панели. Можно задать следующие атрибуты:

- Заголовок, и
- Пользовательский образ или
- Образ системы: принять, добавить, блок, отклонить, Info, возможно, более, отключение звука, приостановка, воспроизведения и повторите, возобновление, общие ресурсы, смешения, говорящего, корзины.

Создание `Action` , выбрав **события** раздел **свойства** панель "и" введите имя для метода действия. Разделяемый метод будет создан в коде, который можно реализовать в классе контроллера интерфейса, следующим образом:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Пользовательские образы

Как к образам вкладку в iOS, это непрозрачный шаблон с альфа-канал, позволяющий фона видны сквозь требуют образов пунктов меню.

Необходимо добавить изображения для меню в проект приложения watch (не watch приложения проекта расширения) для достижения оптимальной производительности.


## <a name="changing-the-menu-items"></a>Изменения элементов меню

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Добавление во время выполнения

Не удается вызвать `Menu` для добавления контроллера интерфейса во время выполнения, несмотря на то что коллекция `MenuItem`s *можно* программно изменить.
Используйте `AddMenuItem` метод, как показано:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

API-Интерфейс Xamarin.iOS Watch Kit в настоящее время требуется `selector` для `AdMenuItem` метод, который должен быть объявлен следующим образом:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Удаление во время выполнения

`ClearAllMenuItems` Метод может вызываться для удаления всех *программного добавления* пунктов меню.

Не удалось очистить элементы меню, настроенные в раскадровке.



## <a name="related-links"></a>Связанные ссылки

- [WatchKitCatalog (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Doc меню Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
