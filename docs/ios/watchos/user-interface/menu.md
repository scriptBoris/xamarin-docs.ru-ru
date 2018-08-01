---
title: watchOS управления Menu (Force Touch) в Xamarin
description: В этом документе описывается использование жеста касания force watchOS в Xamarin. Он описывает, как реагировать на сенсорном force, как добавлять меню и изменение пунктов меню.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 4b973b925b99189416087224644c376864c56871
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791349"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>watchOS управления Menu (Force Touch) в Xamarin

Контрольное значение обеспечивает принудительное Touch жестом, запускающим меню, при реализации на экране приложения Контрольные значения.

![](menu-images/menu.png "Отображение меню Apple Watch")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Отклик на сенсорный ввод Force

Если `Menu` реализован интерфейс контроллера, когда пользователь выполняет принудительное Touch меню будет отображаться. Если меню не был реализован, кратко анимировано экрана никакие другие действия не выполняются.

Force штрихи не связаны с любой определенный элемент на экране; только одно меню можно подключить к контроллеру интерфейса, и он отобразится независимо от того, где происходит клавишу Force Touch на экране.

Параметры могут быть представлены от одного до четырех меню.


## <a name="adding-a-menu"></a>Добавление меню

Объект `Menu` должны добавляться `InterfaceController` раскадровка во время разработки. При перетаскивании элемента управления menu на к контроллеру интерфейса никак не visual Просмотр раскадровки, но **меню** отображается в **Структура документа** заполнения:

![](menu-images/menu-action.png "Изменение меню во время разработки")

Не более четырех меню можно добавлять элементы для элемента управления меню. Они могут быть настроены в **свойства** панель. Можно задать следующие атрибуты:

- Заголовок, и
- Настраиваемый образ, или
- Образ системы: принять, добавить, блок, отклонить, сведения, возможно, более, отключение звука, приостановка, воспроизведения и повторите Resume, общие ресурсы, смещения, говорящего, корзины.

Создание `Action` , выбрав **событий** раздел **свойства** прокладку и ввести имя для метода действия. Разделяемый метод будут созданы в коде, который можно реализовать в классе контроллера интерфейс следующим образом:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Пользовательские изображения

Как вкладка изображений в iOS, является непрозрачным шаблоном с альфа-канал, позволяющий фона видны сквозь требуется изображений пунктов меню.

Следует добавлять изображения, используемые для меню в проект приложения Контрольные значения (не Контрольное значение приложении проекта расширения) для достижения оптимальной производительности.


## <a name="changing-the-menu-items"></a>Изменение пунктов меню

<!--
### Design Time Items

Menu items added the the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Добавление во время выполнения

Не удается вызвать `Menu` для добавления к контроллеру интерфейса во время выполнения, несмотря на то что коллекция `MenuItem`s *можно* программно изменить.
Использование `AddMenuItem` метода, как показано:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

API комплект средств для наблюдения за Xamarin.iOS в настоящее время требует `selector` для `AdMenuItem` метод, который должен быть объявлен следующим образом:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Удаление во время выполнения

`ClearAllMenuItems` Метод может вызываться для удаления всех *программно добавлены* пунктов меню.

Невозможно очистить элементы меню, настроенные в раскадровку.



## <a name="related-links"></a>Связанные ссылки

- [WatchKitCatalog (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Doc меню Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
