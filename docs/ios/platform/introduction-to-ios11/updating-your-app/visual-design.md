---
title: Обновления визуальных элементов разработки в iOS 11
description: В этом документе описываются визуального проектирования, обновлений, представленная в iOS 11. Здесь рассматриваются изменения в панели навигации, поиска контроллеров, поля, содержимое на весь экран и представления таблицы.
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: c6351f2c25f8e31181c761aea1b471315a8a05e8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114522"
---
# <a name="visual-design-updates-in-ios-11"></a>Обновления визуальных элементов разработки в iOS 11

В iOS 11 Apple представила новые визуальные изменения, включая обновления для панели навигации, панель поиска и представления таблицы. Кроме были улучшены для обеспечения дополнительной гибкости поля и содержимое во весь экран. В этом руководстве рассматриваются эти изменения. 

Просмотрите сведения о проектировании для iPhone X, Apple [проектирование для iPhone X](https://developer.apple.com/videos/play/fall2017/801/) видео.

## <a name="uikit"></a>UIKit

UIKit полосы соответствуют в iOS 11, чтобы сделать их более доступными для конечных пользователей.

Одно из этих изменений является новой отображения HUD, которое появляется, когда пользователь долго нажатий на панели элементов. Чтобы включить эту функцию, задайте `largeContentSizeImage` свойство `UIBarItem` и добавление большего размера изображения с помощью [каталог активов](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>Панель переходов
iOS 11 появились новые функции для упрощения чтения заголовков панели навигации. Приложениям отображать в данной книге большего размера, назначив `PrefersLargeTitles` присваивается значение true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

Делает параметр большего размера заголовков в приложении _все_ заголовки полосы навигации в приложении размер больше, как показано на следующем снимке экрана:

![Заголовок больших навигации](visual-design-images/image7.png)

Чтобы контролировать, когда большой заголовок отображается на панели навигации, присвойте `LargeTitleDisplayMode` элемент навигации для `Always`, `Never`, или `Automatic`.

### <a name="search-controller"></a>Поиск контроллера

iOS 11 стало проще добавить контроллер поиска непосредственно в панели навигации. После создания контроллера поиска, добавить его на панель навигации с `SearchController` свойство:

```csharp
NavigationItem.SearchController = searchController;
```

[![Заголовок больших навигации по панели поиска](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

В зависимости от функциональных возможностей приложения могут или не пожелать панели поиска, чтобы скрыть при прокрутке пользователем списка. Это можно изменить с помощью `HidesSearchBarWhenScrolling` свойство.

## <a name="margins"></a>Поля

Apple создала новое свойство — `directionalLayoutMargins` — который может использоваться для задания пространства между представлений и вложенных представлений. Используйте `directionalLayoutMargins` с `leading` или `trailing` отступы. Независимо от того, является ли система с языком слева направо или справа налево интервал в приложении задается соответствующим образом iOS.

В iOS 10 и более ранних все представления имеет размер минимальный размер полей, к которому будет соответствовать. iOS 11 появилась возможность переопределить, используя `ViewRespectsSystemMinimumLayoutMargins`. Например этому свойству значения false позволяет настраивать отступы в edge до нуля:

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```
![Поле отображение изображения с нуля отступа в ios 11](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>Содержимое во весь экран

iOS 7 [появился](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` и `bottomLayoutGuide` как способ ограничить свои представления, чтобы они не скрыты по UIKit полосы и находятся в видимой области экрана. Они стали нерекомендуемыми в iOS 11 для _безопасной области_.

Безопасной области — это новый способ мышления о видимое пространство вашего приложения и способ добавления ограничений между представлением и представления super. Например рассмотрим ниже:

[![Vs безопасной области верхней и нижней направляющая макета](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

Ранее, если добавлено представление и хотели, чтобы быть видимым в зеленая области выше, вы бы ограничить его _нижней_ из `TopLayoutGuide` и _верхней_ из `BottomLayoutGuide`. В iOS 11 будет вместо его, чтобы ограничить _верхней_ и _нижней_ из безопасной области. См. пример ниже:

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>Табличное представление

UITableView имел ряд небольших, но важных изменений в iOS 11.

По умолчанию заголовки, нижние колонтитулы и ячейки теперь автоматически подгоняются по их содержимому. Чтобы отказаться от этого поведения автоматического изменения размера набора `EstimatedRowHeight`, `EstimatedSectionHeaderHeight`, или `EstimatedSectionFooterHeight` до нуля.

Тем не менее в некоторых случаях (например, при добавлении UITableViewController в конструкторе iOS или при использовании существующего Storboards в построителе интерфейса) может потребоваться вручную включить функцию самостоятельного изменения размера ячеек. Чтобы сделать это, убедитесь, что вы указали следующие свойства в представлении таблицы для ячеек, верхние и нижние колонтитулы, соответственно:

```csharp
// Cells
TableView.RowHeight = UITableView.AutomaticDimension;
TableView.EstimatedRowHeight = UITableView.AutomaticDimension;

// Header
TableView.SectionHeaderHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionHeaderHeight = 40f;

//Footer
TableView.SectionFooterHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionFooterHeight = 40f;

```

iOS 11 был расширен функциональные возможности действия строки. `UISwipeActionsConfiguration` была введена для определения набора действий, которые должно быть выполнено, когда пользователь предъявляет в любом направлении над строкой в табличном представлении. Это поведение аналогична собственного Mail.app. Дополнительные сведения см. в разделе [действиями строк](~/ios/user-interface/controls/tables/row-action.md) руководства.

Представления таблиц имеют поддержку перетаскивания в iOS 11. Дополнительные сведения см. в разделе [путем перетаскивания](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) руководства.


## <a name="related-links"></a>Связанные ссылки

- [Новые возможности в iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Страница продукта обновленные App Store (Apple)](https://developer.apple.com/app-store/product-page/)
- [Проектирование для iPhone X (Apple) (видео)](https://developer.apple.com/videos/play/fall2017/801/)
- [Обновление приложения для iOS 11 (WWDC) (видео)](https://developer.apple.com/videos/play/wwdc2017/204/)

