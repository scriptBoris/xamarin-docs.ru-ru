---
title: Представления коллекций в Xamarin.iOS
description: Представления коллекций разрешить доступ к содержимому будет отображаться с использованием произвольного макетов. Они позволяют легко компоновке сеткоподобной по умолчанию, а также пользовательские макеты.
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 8e114f39a01c2243889c53c855acd7fa8061ed09
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114734"
---
# <a name="collection-views-in-xamarinios"></a>Представления коллекций в Xamarin.iOS

_Представления коллекций разрешить доступ к содержимому будет отображаться с использованием произвольного макетов. Они позволяют легко компоновке сеткоподобной по умолчанию, а также пользовательские макеты._

Представления коллекций, доступных в `UICollectionView` класса, — это новое понятие в iOS 6, будет представлен представлении несколько элементов на экране с помощью макетов. Шаблоны для предоставления данных для `UICollectionView` для создания элементов и взаимодействовать с выполните эти элементы, же делегирования и источника данных шаблоны, обычно используемых в разработке iOS.

Тем не менее, представления коллекций работают с подсистемой макет, который является независимым от `UICollectionView` сам. Таким образом просто обеспечивают структуру можно легко изменить представление представлением коллекции.

iOS предоставляет макет класс с именем `UICollectionViewFlowLayout` , позволяющий макетов на основе строки, например сетку, которая будет создаваться без дополнительных действий. Кроме того пользовательские макеты можно также создать, позволяющие любой презентации, которую можно себе представить.

## <a name="uicollectionview-basics"></a>Основы UICollectionView

`UICollectionView` Класс состоит из трех различных элементов:

-  **Ячейки** — представления на основе данных для каждого элемента
-  **С дополнительными представлениями** — управляемые данными представлений, связанных с разделом.
-  **Оформление представления** — на основе представлений, созданных макета Non данных

## <a name="cells"></a>Ячейки

Ячейки являются объекты, представляющие один элемент в наборе данных, который представляется в представлении коллекции. Каждая ячейка представляет собой экземпляр `UICollectionViewCell` класс, который состоит из трех различных представлениях, как показано на рисунке ниже:

 [![](uicollectionview-images/01-uicollectionviewcell.png "Каждая ячейка представляет собой три разных представления, как показано ниже")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

`UICollectionViewCell` Класс имеет следующие свойства для каждого из этих представлений:

-   `ContentView` — Это представление содержит содержимое, которое представляет ячейку. Он отображается в верхнем z порядке на экране.
-   `SelectedBackgroundView` — Ячейки имеют встроенную поддержку для выбора. Это представление используется для визуального обозначения к ячейке. Он отображается под `ContentView` при выборе ячейки.
-   `BackgroundView` — Ячеек можно также отобразить фона, который представлен фигурой `BackgroundView` . В этом представлении отображается под `SelectedBackgroundView` .


Установив `ContentView` таким образом, что оно меньше `BackgroundView` и `SelectedBackgroundView`, `BackgroundView` можно использовать для визуального содержимого, while `SelectedBackgroundView` будет отображаться при выборе ячейки, как показано ниже:

 [![](uicollectionview-images/02-cells.png "Элементы другую ячейку")](uicollectionview-images/02-cells.png#lightbox)

Ячейки на снимке экрана выше создаются путем наследования от `UICollectionViewCell` и параметр `ContentView`, `SelectedBackgroundView` и `BackgroundView` свойства, соответственно, как показано в следующем коде:

```csharp
public class AnimalCell : UICollectionViewCell
{
        UIImageView imageView;

        [Export ("initWithFrame:")]
        public AnimalCell (CGRect frame) : base (frame)
        {
            BackgroundView = new UIView{BackgroundColor = UIColor.Orange};

            SelectedBackgroundView = new UIView{BackgroundColor = UIColor.Green};

            ContentView.Layer.BorderColor = UIColor.LightGray.CGColor;
            ContentView.Layer.BorderWidth = 2.0f;
            ContentView.BackgroundColor = UIColor.White;
            ContentView.Transform = CGAffineTransform.MakeScale (0.8f, 0.8f);

            imageView = new UIImageView (UIImage.FromBundle ("placeholder.png"));
            imageView.Center = ContentView.Center;
            imageView.Transform = CGAffineTransform.MakeScale (0.7f, 0.7f);

            ContentView.AddSubview (imageView);
        }

        public UIImage Image {
            set {
                imageView.Image = value;
            }
        }
}
```

 <a name="Supplementary_Views" />


## <a name="supplementary-views"></a>Представления, с дополнительными символами

С дополнительными представления — представления сведений, связанных с каждым разделом `UICollectionView`. Как и ячеек с дополнительными представления — это управляемые данными. Там, где ячейки представления элемента данных из источника данных, с дополнительными представлениями представления данных раздела, например категории книги на книжной полке или жанр музыки в библиотеке музыки.

Например с дополнительными представления может использоваться для представления заголовок для определенного раздела, как показано на рисунке ниже:

 [![](uicollectionview-images/02a-supplementary-view.png "С дополнительными представление использовать для представления заголовок для определенного раздела, как показано ниже")](uicollectionview-images/02a-supplementary-view.png#lightbox)

Использование дополнительных представления, его сначала необходимо зарегистрировать в `ViewDidLoad` метод:

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

Затем представлению необходимо получить с помощью `GetViewForSupplementaryElement`, созданный с помощью `DequeueReusableSupplementaryView`и наследует от `UICollectionReusableView`. В следующем фрагменте кода будет создавать SupplementaryView, показано на снимке экрана выше:

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

С дополнительными представлениями являются более универсальными, чем просто верхние и нижние колонтитулы.
Они могут размещаться в любом в представлении коллекции и может состоять из любого представления, делая их внешний вид полностью настраиваемые возможности.

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>Оформление представления

Оформление представления являются чисто визуальных представлений, которые могут отображаться в `UICollectionView`. В отличие от ячеек и дополнительных представления они являются не управляемые данными. Они всегда создаются в подкласс макета и впоследствии можно изменить как макет содержимого. К примеру, оформление представления может быть использован для представления фона представление, которое выполняет прокрутку с содержимым в `UICollectionView`, как показано ниже:

 [![](uicollectionview-images/02c-decoration-view.png "Представление оформления с красным фоном")](uicollectionview-images/02c-decoration-view.png#lightbox)

 Приведенный ниже фрагмент кода изменяет фона на красный, в примерах `CircleLayout` класса:

 ```csharp
 public class MyDecorationView : UICollectionReusableView
    {
        [Export ("initWithFrame:")]
        public MyDecorationView (CGRect frame) : base (frame)
        {
            BackgroundColor = UIColor.Red;
        }
    }
 ```


## <a name="data-source"></a>источника данных

Как и в других частях iOS, такие как `UITableView` и `MKMapView`, `UICollectionView` получает данные от *источника данных*, предоставляемый в Xamarin.iOS с помощью **`UICollectionViewDataSource`** класса. Этот класс отвечает за предоставление содержимого `UICollectionView` такие как:

-  **Ячейки** — возвращаемые `GetCell` метод.
-  **С дополнительными представлениями** — возвращаемые `GetViewForSupplementaryElement` метод.
-  **Число разделов** — возвращаемые `NumberOfSections` метод. По умолчанию используется значение 1, если не реализовано.
-  **Число элементов на разделе** — возвращаемые `GetItemsCount` метод.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
Для удобства `UICollectionViewController` класс доступен. Это автоматически настроено как делегат, который рассматривается в следующем разделе, и источник данных для его `UICollectionView` представления.

Как и в `UITableView`, `UICollectionView` класса будет вызывать только источника данных, чтобы получить ячейки для элементов на экране.
Ячейки, которые можно прокручивать на экране помещаются в очередь для повторного использования, как показано на следующем рисунке:

 [![](uicollectionview-images/03-cell-reuse.png "Ячейки, которые можно прокручивать на экране помещаются в очередь для повторного использования, как показано ниже")](uicollectionview-images/03-cell-reuse.png#lightbox)

Повторное использование ячеек упрощен благодаря `UICollectionView` и `UITableView`. Больше не требуется для создания ячейки непосредственно в источнике данных, если он не в очереди повторного использования, как ячейки регистрируются в системе. Если ячейки не доступен при выполнении вызова из очереди ячейки из очереди повторного использования, то iOS создаст ее автоматически на основе типа или nib, который был зарегистрирован.
Та же технология также доступна для представления, с дополнительными символами.

Например, рассмотрим следующий код, который регистрирует `AnimalCell` класса:

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

Когда `UICollectionView` должен ячейку, так как соответствующий элемент на экране `UICollectionView` вызывает источника данных `GetCell` метод. Подобно тому как это работает с UITableView, этот метод отвечает за настройку ячейке резервных данных, которое было бы `AnimalCell` в этом случае.

Следующий код показывает реализацию `GetCell` , возвращающий `AnimalCell` экземпляр:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

Вызов `DequeReusableCell` — где ячейка будет иметь либо из очереди из очереди повторного использования, или, если ячейка не содержится в очереди, созданные в зависимости от типа, зарегистрированных в вызове `CollectionView.RegisterClassForCell`.

Таким образом, зарегистрировав `AnimalCell` класс, iOS будет создана новая `AnimalCell` внутренне и возвратить его при вызове из очереди ячейки, после чего он использует образ, который содержится в классе «Animal» и возврате для отображения для `UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>делегат

`UICollectionView` Класс использует делегат типа `UICollectionViewDelegate` для поддержки взаимодействия с содержимым в `UICollectionView`. Это позволяет контролировать:

-  **Выбор ячейки** — определяет, если ячейка выбрана.
-  **Выделение ячеек** — определяет, если ячейки в настоящее время затрагиваемых.
-  **Ячейки меню** — меню, которое выводится для ячейки в ответ на жест последовательности при долгом нажатии.


Как и в случае с источником данных, `UICollectionViewController` настроен по умолчанию считается делегат для `UICollectionView`.

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>Выделение ячеек

При нажатии ячейки, переходы в состояние с выделенной ячейки, он не установлен до их палец отрывается из ячейки. Благодаря этому временные изменения внешнего вида ячейки до ее фактического выбора. При выборе соответствующего пункта, ячейки в `SelectedBackgroundView` отображается. На рисунке ниже показаны выделенные состояние непосредственно перед Выбор выполняется:

 [![](uicollectionview-images/04-cell-highlight.png "На рисунке выше показан выделенный состояние непосредственно перед Выбор выполняется")](uicollectionview-images/04-cell-highlight.png#lightbox)

Для реализации выделение `ItemHighlighted` и `ItemUnhighlighted` методы `UICollectionViewDelegate` может использоваться. Например, следующий код будет применено желтый фон `ContentView` при выделении ячейки и белый фон, если не видно, как показано на рисунке выше:

```csharp
public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.Yellow;
}

public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.White;
}
```

 <a name="Disabling_Selection" />


#### <a name="disabling-selection"></a>Отключение выделения

Выбор включен по умолчанию в `UICollectionView`. Чтобы отключить выбор, переопределите `ShouldHighlightItem` и возвращает значение false, как показано ниже:

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

При отключении выделение процесс выбора ячейки также отключен. Кроме того, имеется также `ShouldSelectItem` метод, который управляет выбора напрямую, хотя если `ShouldHighlightItem` реализуется и возвращает значение false, `ShouldSelectItem` не вызывается.

 `ShouldSelectItem` позволяет выбрать включить или отключить для отдельных по элементу, когда `ShouldHighlightItem` не реализован. Она также позволяет выделение без выбора, если `ShouldHighlightItem` реализуется и возвращает значение true, при `ShouldSelectItem` возвращает значение false.

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>Меню ячейки

Каждая ячейка в `UICollectionView` способен отображать меню, которое позволяет вырезания, копирования и вставки, которые должны поддерживаться при необходимости. Чтобы создать меню "Правка" на ячейку:

1.  Переопределить `ShouldShowMenu` и возвращают значение true, если элемент должен Показать меню.
1.  Переопределить `CanPerformAction` и возвращают значение true для каждой действие, которое элемент, который будет иметь одно из операции вырезания, копирования или вставки.
1.  Переопределить `PerformAction` Чтобы выполнить редактирование, копирование операции вставки.


Следующий снимок экрана отображения меню при нажатии долго ячейки:

 [![](uicollectionview-images/04a-menu.png "На этом снимке экрана показано меню долго нажатой ячейки")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>Макет

`UICollectionView` поддерживает макета, которая позволяет располагать все его элементы, ячеек, с дополнительными представлениями и оформления представления, управляться независимо от `UICollectionView` сам.
С помощью системы макета, приложение может поддерживать макетов, таких как тот сеткоподобной мы видели в этой статье, а также предоставить пользовательские макеты.

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>Основы

Макеты в `UICollectionView` определяются в классе, который наследует от `UICollectionViewLayout`. Реализация макета отвечает за создание атрибутов макета для каждого элемента в `UICollectionView`. Существует два способа для создания макета:

-  Используется встроенная `UICollectionViewFlowLayout` .
-  Предоставить настраиваемый макет путем наследования от `UICollectionViewLayout` .


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>Потоковый макет

`UICollectionViewFlowLayout` Класс предоставляет макет, подходящая для упорядочивания содержимого в сетке ячеек, как мы видели на основе строки.

Чтобы использовать потоковый макет:

-  Создайте экземпляр `UICollectionViewFlowLayout` :


```csharp
var layout = new UICollectionViewFlowLayout ();
```

-  Передайте этот экземпляр в конструктор класса `UICollectionView` :


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

Это все, что нужно для макета содержимого в сетке. Кроме того, при изменении ориентации, `UICollectionViewFlowLayout` обрабатывает изменение расположения содержимого, соответствующим образом, как показано ниже:

 [![](uicollectionview-images/05-layout-orientation.png "Пример изменения ориентации")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>Раздел «отступ»

Для предоставления некоторое пространство вокруг `UIContentView`, макеты имеют `SectionInset` свойство типа `UIEdgeInsets`. Например, следующий код предоставляет буфер 50 пикселов вокруг каждого раздела `UIContentView` при изложенные в `UICollectionViewFlowLayout`:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

Это приводит к пробелы вокруг области, как показано ниже:

 [![](uicollectionview-images/06-sectioninset.png "Пробелы вокруг раздела, как показано ниже")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>Создание подкласса UICollectionViewFlowLayout

В выпуске с помощью `UICollectionViewFlowLayout` напрямую, он может также быть подклассом для дальнейшей настройки макета содержимого вдоль линии. Например это может использоваться для создания макета, не переносится в сетку ячеек, но вместо этого создает одну строку с эффектом горизонтальной прокрутки, как показано ниже:

 [![](uicollectionview-images/07-line-layout.png "Одну строку с эффектом горизонтальной прокрутки")](uicollectionview-images/07-line-layout.png#lightbox)

Чтобы реализовать это путем создания подклассов `UICollectionViewFlowLayout` требуется:

-  Инициализация, любые свойства макета, которые применяются к сам макет или все элементы в макет в конструкторе.
-  Переопределение `ShouldInvalidateLayoutForBoundsChange` и возвращает значение true, так что при границы `UICollectionView` изменения, макет ячейки будут пересчитаны. Это используется в этом случае убедитесь код для преобразования, применяемый к ячейке centermost, которые будут применяться во время прокрутки.
-  Переопределение `TargetContentOffset` вносить centermost ячейки привязки в центр `UICollectionView` как прокрутка останавливается.
-  Переопределение `LayoutAttributesForElementsInRect` для возвращения массива из `UICollectionViewLayoutAttributes` . Каждый `UICollectionViewLayoutAttribute` содержит сведения о том, как макет конкретного элемента, включая свойства, такие как его `Center` , `Size` , `ZIndex` и `Transform3D` .


В следующем коде показано такой реализации:

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;
using CoreGraphics;
using CoreAnimation;

namespace SimpleCollectionView
{
    public class LineLayout : UICollectionViewFlowLayout
    {
        public const float ITEM_SIZE = 200.0f;
        public const int ACTIVE_DISTANCE = 200;
        public const float ZOOM_FACTOR = 0.3f;

        public LineLayout ()
        {
            ItemSize = new CGSize (ITEM_SIZE, ITEM_SIZE);
            ScrollDirection = UICollectionViewScrollDirection.Horizontal;
            SectionInset = new UIEdgeInsets (400,0,400,0);
            MinimumLineSpacing = 50.0f;
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            return true;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var array = base.LayoutAttributesForElementsInRect (rect);
            var visibleRect = new CGRect (CollectionView.ContentOffset, CollectionView.Bounds.Size);

            foreach (var attributes in array) {
                if (attributes.Frame.IntersectsWith (rect)) {
                    float distance = (float)(visibleRect.GetMidX () - attributes.Center.X);
                    float normalizedDistance = distance / ACTIVE_DISTANCE;
                    if (Math.Abs (distance) < ACTIVE_DISTANCE) {
                        float zoom = 1 + ZOOM_FACTOR * (1 - Math.Abs (normalizedDistance));
                        attributes.Transform3D = CATransform3D.MakeScale (zoom, zoom, 1.0f);
                        attributes.ZIndex = 1;
                    }
                }
            }
            return array;
        }

        public override CGPoint TargetContentOffset (CGPoint proposedContentOffset, CGPoint scrollingVelocity)
        {
            float offSetAdjustment = float.MaxValue;
            float horizontalCenter = (float)(proposedContentOffset.X + (this.CollectionView.Bounds.Size.Width / 2.0));
            CGRect targetRect = new CGRect (proposedContentOffset.X, 0.0f, this.CollectionView.Bounds.Size.Width, this.CollectionView.Bounds.Size.Height);
            var array = base.LayoutAttributesForElementsInRect (targetRect);
            foreach (var layoutAttributes in array) {
                float itemHorizontalCenter = (float)layoutAttributes.Center.X;
                if (Math.Abs (itemHorizontalCenter - horizontalCenter) < Math.Abs (offSetAdjustment)) {
                    offSetAdjustment = itemHorizontalCenter - horizontalCenter;
                }
            }
            return new CGPoint (proposedContentOffset.X + offSetAdjustment, proposedContentOffset.Y);
        }

    }
}
```

 <a name="Custom_Layout" />


### <a name="custom-layout"></a>Пользовательский макет

Помимо использования `UICollectionViewFlowLayout`, макеты можно также полностью настроить путем наследования непосредственно из `UICollectionViewLayout`.

Ниже приведены основные методы для переопределения.

-   `PrepareLayout` — Используется для выполнения начальной геометрических вычислений, которые будут использоваться на протяжении всего процесса макета.
-   `CollectionViewContentSize` — Возвращает размер области, используемый для отображения содержимого.
-   `LayoutAttributesForElementsInRect` — Как в примере UICollectionViewFlowLayout, показанный ранее, этот метод используется для предоставления сведений о `UICollectionView` о том, как макет каждого элемента. Однако в отличие от `UICollectionViewFlowLayout` , при создании пользовательского макета, но выбрать можно разместить элементы.


Например то же содержимое можно представить в циклическая макета, как показано ниже:

 [![](uicollectionview-images/08-circle-layout.png "Циклическая пользовательского макета, как показано ниже")](uicollectionview-images/08-circle-layout.png#lightbox)

Зрелище о макетах является то, что для изменения из макета сетки, в структуру горизонтальной прокрутки и этот циклическая макет требует только класс макета для `UICollectionView` изменить. Nothing в `UICollectionView`, его делегата или источника данных изменения в код вообще.


## <a name="changes-in-ios-9"></a>Изменения в iOS 9


В iOS 9, представление коллекции (`UICollectionView`) теперь поддерживает перетащите изменение порядка элементов без дополнительной настройки, добавив новый распознаватель жестов по умолчанию и несколько новых вспомогательных методов.

Используя эти новые методы, можно легко реализовать перетащите, чтобы изменить порядок представления коллекции и существует возможность настройки внешнего вида элементов во время любой этап процесса изменения порядка.

[![](uicollectionview-images/intro01.png "Пример изменения порядка процесса")](uicollectionview-images/intro01.png#lightbox)

В этой статье мы рассмотрим реализация перетаскивания для изменения порядка в приложение Xamarin.iOS, а также некоторые другие изменения, внесенные в iOS 9 в элементе управления представления коллекции:

- [Простое изменение порядка элементов](#Easy-Reordering-of-Items)
    - [Простой пример изменения порядка](#Simple-Reordering-Example)
    - [С помощью пользовательского распознавателя](#Using-a-Custom-Gesture-Recognizer)
    - [Пользовательские макеты и изменение порядка](#Custom-Layouts-and-Reording)
- [Изменение представления коллекции](#Collection-View-Changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>Изменение порядка элементов

Как уже говорилось выше, одним из наиболее значимых изменений к представлению коллекции в iOS 9 была Добавление готовые просто перетащите для изменения порядка функциональные возможности.

В iOS 9, самый быстрый способ добавления, изменения порядка для представления коллекции является использование `UICollectionViewController`.
Контроллер представления коллекций теперь имеет `InstallsStandardGestureForInteractiveMovement` свойство, которое добавляет стандартный *распознаватель жестов* , поддерживающий перетаскивание для изменения порядка элементов в коллекции.
Так как значение по умолчанию — `true`, необходимо реализовать `MoveItem` метод `UICollectionViewDataSource` класс для поддержки перетаскивания для изменения порядка. Пример:

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    // Reorder our list of items
    ...
}
```
<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>Простой пример изменения порядка

В качестве примера быстрого запуска проекта Xamarin.iOS и редактирования **Main.storyboard** файла. Перетащите `UICollectionViewController` в область конструктора:

[![](uicollectionview-images/quick01.png "Добавление UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

Выберите представление коллекции (он может быть проще всего этого в структуре документа). На вкладке "Макет" панели свойств задайте следующие размеры, как показано на следующем снимке экрана:

- **Размер ячейки**: ширина – 60 | Высота — 60
- **Размер заголовка**: ширина – 0 | Height: 0
- **Размер нижнего колонтитула**: ширина – 0 | Height: 0
- **Интервал Min**: для ячейки — 8 | Строки – 8
- **Раздел отступы**: 16 верхних | Нижней – 16 | Left — 16 | Справа — 16

[![](uicollectionview-images/quick04.png "Задать размеры представления коллекции")](uicollectionview-images/quick04.png#lightbox)

Измените значение по умолчанию ячейки:
    - Изменить цвет фона на синий
    - Добавьте метку в качестве заголовка для ячейки
    - Значение идентификатора повторное использование **ячейки**

[![](uicollectionview-images/quick02.png "Замените заданную по умолчанию ячейки")](uicollectionview-images/quick02.png#lightbox)

Добавление ограничений для поддержания метки по центру ячейки как изменяется размер:

В **панели свойство** для _CollectionViewCell_ и задайте **класс** для `TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "Задайте класс TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

Задайте **многократного использования представления коллекции** для `Cell`:

[![](uicollectionview-images/quick06.png "Значение ячейки для повторного использования представления коллекции")](uicollectionview-images/quick06.png#lightbox)

Наконец, выберите метку и назовите его `TextLabel`:

[![](uicollectionview-images/quick07.png "Метка имени TextLabel")](uicollectionview-images/quick07.png#lightbox)

Изменить `TextCollectionViewCell` класса и добавьте следующие свойства.:

```csharp
using System;
using Foundation;
using UIKit;

namespace CollectionView
{
    public partial class TextCollectionViewCell : UICollectionViewCell
    {
        #region Computed Properties
        public string Title {
            get { return TextLabel.Text; }
            set { TextLabel.Text = value; }
        }
        #endregion

        #region Constructors
        public TextCollectionViewCell (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Здесь `Text` метки указывается в виде заголовка ячейки, поэтому его можно установить из кода.

Добавьте новый C# класса в проект и назовите его `WaterfallCollectionSource`. Измените файл и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionSource : UICollectionViewDataSource
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        public List<int> Numbers { get; set; } = new List<int> ();
        #endregion

        #region Constructors
        public WaterfallCollectionSource (WaterfallCollectionView collectionView)
        {
            // Initialize
            CollectionView = collectionView;

            // Init numbers collection
            for (int n = 0; n < 100; ++n) {
                Numbers.Add (n);
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView) {
            // We only have one section
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section) {
            // Return the number of items
            return Numbers.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a reusable cell and set {~~it's~>its~~} title from the item
            var cell = collectionView.DequeueReusableCell ("Cell", indexPath) as TextCollectionViewCell;
            cell.Title = Numbers [(int)indexPath.Item].ToString();

            return cell;
        }

        public override bool CanMoveItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // We can always move items
            return true;
        }

        public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
        {
            // Reorder our list of items
            var item = Numbers [(int)sourceIndexPath.Item];
            Numbers.RemoveAt ((int)sourceIndexPath.Item);
            Numbers.Insert ((int)destinationIndexPath.Item, item);
        }
        #endregion
    }
}
```

Этот класс будет источником данных для нашего представления коллекции и предоставить сведения для каждой ячейки в коллекции.
Обратите внимание, что `MoveItem` реализованный метод для предоставления перетащите элементы в коллекции, чтобы быть переупорядоченных.

Добавьте еще один новый C# класса в проект и назовите его `WaterfallCollectionDelegate`. Измените этот файл и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionDelegate : UICollectionViewDelegate
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        #endregion

        #region Constructors
        public WaterfallCollectionDelegate (WaterfallCollectionView collectionView)
        {

            // Initialize
            CollectionView = collectionView;

        }
        #endregion

        #region Overrides Methods
        public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // Always allow for highlighting
            return true;
        }

        public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and change to green background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(183,208,57);
        }

        public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and return to blue background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(164,205,255);
        }
        #endregion
    }
}
```

Это будет выступать в качестве делегата для нашего представления коллекции. Методы были переопределены для выделения ячейки, так как пользователь взаимодействует с ним в представлении коллекции.

Добавьте один, последний C# класса в проект и назовите его `WaterfallCollectionView`. Измените этот файл и это должно выглядеть следующим образом:

```csharp
using System;
using UIKit;
using System.Collections.Generic;
using Foundation;

namespace CollectionView
{
    [Register("WaterfallCollectionView")]
    public class WaterfallCollectionView : UICollectionView
    {

        #region Constructors
        public WaterfallCollectionView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Initialize
            DataSource = new WaterfallCollectionSource(this);
            Delegate = new WaterfallCollectionDelegate(this);

        }
        #endregion
    }
}
```

Обратите внимание, что `DataSource` и `Delegate` , созданным ранее задаются, когда представление коллекции создается на основе его раскадровки (или **.xib** файла).

Изменить **Main.storyboard** файл еще раз и выберите представление коллекции и переключиться в режим **свойства**. Задайте **класс** с пользовательскими `WaterfallCollectionView` класс, определенный выше:

Сохраните изменения, внесенные в пользовательском Интерфейсе и запустить приложение.
Если пользователь выбирает элемент в списке и перетаскивает его в новое расположение, остальные элементы будут анимация автоматически при переходе в сторону элемента.
Когда пользователь перетаскивает элемент в новое расположение, он сохранится в это расположение. Пример:

[![](uicollectionview-images/intro01.png "Пример перетаскивания элемента в новое расположение")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>С помощью пользовательского распознавателя

В случаях, когда невозможно использовать `UICollectionViewController` и необходимо использовать обычный `UIViewController`, или если требуется больший контроль над жестов перетаскивания и вставки, можно создать свой собственный пользовательский распознаватель жестов и добавьте его к представлению коллекции после загрузки представления. Пример:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Create a custom gesture recognizer
    var longPressGesture = new UILongPressGestureRecognizer ((gesture) => {

        // Take action based on state
        switch(gesture.State) {
        case UIGestureRecognizerState.Began:
            var selectedIndexPath = CollectionView.IndexPathForItemAtPoint(gesture.LocationInView(View));
            if (selectedIndexPath !=null) {
                CollectionView.BeginInteractiveMovementForItem(selectedIndexPath);
            }
            break;
        case UIGestureRecognizerState.Changed:
            CollectionView.UpdateInteractiveMovementTargetPosition(gesture.LocationInView(View));
            break;
        case UIGestureRecognizerState.Ended:
            CollectionView.EndInteractiveMovement();
            break;
        default:
            CollectionView.CancelInteractiveMovement();
            break;
        }

    });

    // Add the custom recognizer to the collection view
    CollectionView.AddGestureRecognizer(longPressGesture);
}
```

Здесь мы используем несколько новых методов, которые добавляются в представление коллекции для внедрения и управления операции перетаскивания:

 - `BeginInteractiveMovementForItem` — Помечает начало операции перемещения.
 - `UpdateInteractiveMovementTargetPosition` -Отправляется по мере обновления расположение этого элемента.
 - `EndInteractiveMovement` -Отмечает конец перемещения элемента.
 - `CancelInteractiveMovement` — Помечает пользовательской отменой операции перемещения.

При запуске приложения, операция перетаскивания будет работать аналогично по умолчанию перетащите распознаватель жестов, входящий в состав представления коллекции.

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>Пользовательские макеты и изменение порядка

В iOS 9 были добавлены новые методы для работы с перетащите для изменения порядка и пользовательских макетов в представлении коллекции. Чтобы исследовать эту функцию, давайте добавим настраиваемый макет в коллекцию.

Во-первых, добавьте новый C# класс с именем `WaterfallCollectionLayout` в проект. Изменить его и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;
using CoreGraphics;

namespace CollectionView
{
    [Register("WaterfallCollectionLayout")]
    public class WaterfallCollectionLayout : UICollectionViewLayout
    {
        #region Private Variables
        private int columnCount = 2;
        private nfloat minimumColumnSpacing = 10;
        private nfloat minimumInterItemSpacing = 10;
        private nfloat headerHeight = 0.0f;
        private nfloat footerHeight = 0.0f;
        private UIEdgeInsets sectionInset = new UIEdgeInsets(0, 0, 0, 0);
        private WaterfallCollectionRenderDirection itemRenderDirection = WaterfallCollectionRenderDirection.ShortestFirst;
        private Dictionary<nint,UICollectionViewLayoutAttributes> headersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private Dictionary<nint,UICollectionViewLayoutAttributes> footersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private List<CGRect> unionRects = new List<CGRect>();
        private List<nfloat> columnHeights = new List<nfloat>();
        private List<UICollectionViewLayoutAttributes> allItemAttributes = new List<UICollectionViewLayoutAttributes>();
        private List<List<UICollectionViewLayoutAttributes>> sectionItemAttributes = new List<List<UICollectionViewLayoutAttributes>>();
        private nfloat unionSize = 20;
        #endregion

        #region Computed Properties
        [Export("ColumnCount")]
        public int ColumnCount {
            get { return columnCount; }
            set {
                WillChangeValue ("ColumnCount");
                columnCount = value;
                DidChangeValue ("ColumnCount");

                InvalidateLayout ();
            }
        }

        [Export("MinimumColumnSpacing")]
        public nfloat MinimumColumnSpacing {
            get { return minimumColumnSpacing; }
            set {
                WillChangeValue ("MinimumColumnSpacing");
                minimumColumnSpacing = value;
                DidChangeValue ("MinimumColumnSpacing");

                InvalidateLayout ();
            }
        }

        [Export("MinimumInterItemSpacing")]
        public nfloat MinimumInterItemSpacing {
            get { return minimumInterItemSpacing; }
            set {
                WillChangeValue ("MinimumInterItemSpacing");
                minimumInterItemSpacing = value;
                DidChangeValue ("MinimumInterItemSpacing");

                InvalidateLayout ();
            }
        }

        [Export("HeaderHeight")]
        public nfloat HeaderHeight {
            get { return headerHeight; }
            set {
                WillChangeValue ("HeaderHeight");
                headerHeight = value;
                DidChangeValue ("HeaderHeight");

                InvalidateLayout ();
            }
        }

        [Export("FooterHeight")]
        public nfloat FooterHeight {
            get { return footerHeight; }
            set {
                WillChangeValue ("FooterHeight");
                footerHeight = value;
                DidChangeValue ("FooterHeight");

                InvalidateLayout ();
            }
        }

        [Export("SectionInset")]
        public UIEdgeInsets SectionInset {
            get { return sectionInset; }
            set {
                WillChangeValue ("SectionInset");
                sectionInset = value;
                DidChangeValue ("SectionInset");

                InvalidateLayout ();
            }
        }

        [Export("ItemRenderDirection")]
        public WaterfallCollectionRenderDirection ItemRenderDirection {
            get { return itemRenderDirection; }
            set {
                WillChangeValue ("ItemRenderDirection");
                itemRenderDirection = value;
                DidChangeValue ("ItemRenderDirection");

                InvalidateLayout ();
            }
        }
        #endregion

        #region Constructors
        public WaterfallCollectionLayout ()
        {
        }

        public WaterfallCollectionLayout(NSCoder coder) : base(coder) {

        }
        #endregion

        #region Public Methods
        public nfloat ItemWidthInSectionAtIndex(int section) {

            var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
            return (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);
        }
        #endregion

        #region Override Methods
        public override void PrepareLayout ()
        {
            base.PrepareLayout ();

            // Get the number of sections
            var numberofSections = CollectionView.NumberOfSections();
            if (numberofSections == 0)
                return;

            // Reset collections
            headersAttributes.Clear ();
            footersAttributes.Clear ();
            unionRects.Clear ();
            columnHeights.Clear ();
            allItemAttributes.Clear ();
            sectionItemAttributes.Clear ();

            // Initialize column heights
            for (int n = 0; n < ColumnCount; n++) {
                columnHeights.Add ((nfloat)0);
            }

            // Process all sections
            nfloat top = 0.0f;
            var attributes = new UICollectionViewLayoutAttributes ();
            var columnIndex = 0;
            for (nint section = 0; section < numberofSections; ++section) {
                // Calculate section specific metrics
                var minimumInterItemSpacing = (MinimumInterItemSpacingForSection == null) ? MinimumColumnSpacing :
                    MinimumInterItemSpacingForSection (CollectionView, this, section);

                // Calculate widths
                var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
                var itemWidth = (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);

                // Calculate section header
                var heightHeader = (HeightForHeader == null) ? HeaderHeight :
                    HeightForHeader (CollectionView, this, section);

                if (heightHeader > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Header, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, heightHeader);
                    headersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);

                    top = attributes.Frame.GetMaxY ();
                }

                top += SectionInset.Top;
                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }

                // Calculate Section Items
                var itemCount = CollectionView.NumberOfItemsInSection(section);
                List<UICollectionViewLayoutAttributes> itemAttributes = new List<UICollectionViewLayoutAttributes> ();

                for (nint n = 0; n < itemCount; n++) {
                    var indexPath = NSIndexPath.FromRowSection (n, section);
                    columnIndex = NextColumnIndexForItem (n);
                    var xOffset = SectionInset.Left + (itemWidth + MinimumColumnSpacing) * (nfloat)columnIndex;
                    var yOffset = columnHeights [columnIndex];
                    var itemSize = (SizeForItem == null) ? new CGSize (0, 0) : SizeForItem (CollectionView, this, indexPath);
                    nfloat itemHeight = 0.0f;

                    if (itemSize.Height > 0.0f && itemSize.Width > 0.0f) {
                        itemHeight = (nfloat)Math.Floor (itemSize.Height * itemWidth / itemSize.Width);
                    }

                    attributes = UICollectionViewLayoutAttributes.CreateForCell (indexPath);
                    attributes.Frame = new CGRect (xOffset, yOffset, itemWidth, itemHeight);
                    itemAttributes.Add (attributes);
                    allItemAttributes.Add (attributes);
                    columnHeights [columnIndex] = attributes.Frame.GetMaxY () + MinimumInterItemSpacing;
                }
                sectionItemAttributes.Add (itemAttributes);

                // Calculate Section Footer
                nfloat footerHeight = 0.0f;
                columnIndex = LongestColumnIndex();
                top = columnHeights [columnIndex] - MinimumInterItemSpacing + SectionInset.Bottom;
                footerHeight = (HeightForFooter == null) ? FooterHeight : HeightForFooter(CollectionView, this, section);

                if (footerHeight > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Footer, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, footerHeight);
                    footersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);
                    top = attributes.Frame.GetMaxY ();
                }

                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }
            }

            var i =0;
            var attrs = allItemAttributes.Count;
            while(i < attrs) {
                var rect1 = allItemAttributes [i].Frame;
                i = (int)Math.Min (i + unionSize, attrs) - 1;
                var rect2 = allItemAttributes [i].Frame;
                unionRects.Add (CGRect.Union (rect1, rect2));
                i++;
            }

        }

        public override CGSize CollectionViewContentSize {
            get {
                if (CollectionView.NumberOfSections () == 0) {
                    return new CGSize (0, 0);
                }

                var contentSize = CollectionView.Bounds.Size;
                contentSize.Height = columnHeights [0];
                return contentSize;
            }
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForItem (NSIndexPath indexPath)
        {
            if (indexPath.Section >= sectionItemAttributes.Count) {
                return null;
            }

            if (indexPath.Item >= sectionItemAttributes [indexPath.Section].Count) {
                return null;
            }

            var list = sectionItemAttributes [indexPath.Section];
            return list [(int)indexPath.Item];
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForSupplementaryView (NSString kind, NSIndexPath indexPath)
        {
            var attributes = new UICollectionViewLayoutAttributes ();

            switch (kind) {
            case "header":
                attributes = headersAttributes [indexPath.Section];
                break;
            case "footer":
                attributes = footersAttributes [indexPath.Section];
                break;
            }

            return attributes;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var begin = 0;
            var end = unionRects.Count;
            List<UICollectionViewLayoutAttributes> attrs = new List<UICollectionViewLayoutAttributes> ();


            for (int i = 0; i < end; i++) {
                if (rect.IntersectsWith(unionRects[i])) {
                    begin = i * (int)unionSize;
                }
            }

            for (int i = end - 1; i >= 0; i--) {
                if (rect.IntersectsWith (unionRects [i])) {
                    end = (int)Math.Min ((i + 1) * (int)unionSize, allItemAttributes.Count);
                    break;
                }
            }

            for (int i = begin; i < end; i++) {
                var attr = allItemAttributes [i];
                if (rect.IntersectsWith (attr.Frame)) {
                    attrs.Add (attr);
                }
            }

            return attrs.ToArray();
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            var oldBounds = CollectionView.Bounds;
            return (newBounds.Width != oldBounds.Width);
        }
        #endregion

        #region Private Methods
        private int ShortestColumnIndex() {
            var index = 0;
            var shortestHeight = nfloat.MaxValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height < shortestHeight) {
                    shortestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int LongestColumnIndex() {
            var index = 0;
            var longestHeight = nfloat.MinValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height > longestHeight) {
                    longestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int NextColumnIndexForItem(nint item) {
            var index = 0;

            switch (ItemRenderDirection) {
            case WaterfallCollectionRenderDirection.ShortestFirst:
                index = ShortestColumnIndex ();
                break;
            case WaterfallCollectionRenderDirection.LeftToRight:
                index = ColumnCount;
                break;
            case WaterfallCollectionRenderDirection.RightToLeft:
                index = (ColumnCount - 1) - ((int)item / ColumnCount);
                break;
            }

            return index;
        }
        #endregion

        #region Events
        public delegate CGSize WaterfallCollectionSizeDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, NSIndexPath indexPath);
        public delegate nfloat WaterfallCollectionFloatDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);
        public delegate UIEdgeInsets WaterfallCollectionEdgeInsetsDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);

        public event WaterfallCollectionSizeDelegate SizeForItem;
        public event WaterfallCollectionFloatDelegate HeightForHeader;
        public event WaterfallCollectionFloatDelegate HeightForFooter;
        public event WaterfallCollectionEdgeInsetsDelegate InsetForSection;
        public event WaterfallCollectionFloatDelegate MinimumInterItemSpacingForSection;
        #endregion
    }
}
```

Это может быть используется класс для предоставления пользовательского два столбца, каскадная диаграмма макета типа представлению коллекции.
В коде используется ключ-значение кодирования (с помощью `WillChangeValue` и `DidChangeValue` методы) для обеспечения привязки данных для вычисляемого свойства в этом классе.

Далее следует изменить `WaterfallCollectionSource` и внесите следующие изменения и дополнения:

```csharp
private Random rnd = new Random();
...

public List<nfloat> Heights { get; set; } = new List<nfloat> ();
...

public WaterfallCollectionSource (WaterfallCollectionView collectionView)
{
    // Initialize
    CollectionView = collectionView;

    // Init numbers collection
    for (int n = 0; n < 100; ++n) {
        Numbers.Add (n);
        Heights.Add (rnd.Next (0, 100) + 40.0f);
    }
}
```

Это создаст случайный рост для каждого из элементов, которые будут отображаться в списке.

Далее следует изменить `WaterfallCollectionView` класса и добавьте следующее свойство вспомогательный:

```csharp
public WaterfallCollectionSource Source {
    get { return (WaterfallCollectionSource)DataSource; }
}
```

Это облегчит получение в наш источник данных (и значения высоты элемента) из пользовательского макета.

Наконец измените контроллер представления и добавьте следующий код:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    var waterfallLayout = new WaterfallCollectionLayout ();

    // Wireup events
    waterfallLayout.SizeForItem += (collectionView, layout, indexPath) => {
        var collection = collectionView as WaterfallCollectionView;
        return new CGSize((View.Bounds.Width-40)/3,collection.Source.Heights[(int)indexPath.Item]);
    };

    // Attach the custom layout to the collection
    CollectionView.SetCollectionViewLayout(waterfallLayout, false);
}
```

Это создает экземпляр класса наших пользовательского макета, устанавливает событие, чтобы обеспечить размер каждого элемента и присоединяет новый макет для нашего представления коллекции.

Если мы снова запустите приложение Xamarin.iOS, представление коллекции теперь будет выглядеть следующим образом:

[![](uicollectionview-images/custom01.png "Представление коллекции теперь будет выглядеть следующим образом")](uicollectionview-images/custom01.png#lightbox)

Мы можем по-прежнему перетащите чтобы переупорядочить элементы как и раньше, но элементы изменится размер в соответствии с учетом их нового местоположения при перетаскивании их.

## <a name="collection-view-changes"></a>Изменение представления коллекции

В следующих разделах мы рассмотрим подробно рассматриваются изменения, внесенные в каждый класс в представлении коллекции iOS 9.

### <a name="uicollectionview"></a>UICollectionView

Чтобы были внесены следующие изменения или дополнения `UICollectionView` класс для iOS 9:

 - `BeginInteractiveMovementForItem` — Помечает начало операции перетаскивания.
 - `CancelInteractiveMovement` — Сообщает коллекции представление, что пользователь отменил операцию перетаскивания.
 - `EndInteractiveMovement` — Сообщает коллекции представление, что пользователь завершил операцию перетаскивания.
 - `GetIndexPathsForVisibleSupplementaryElements` — Возвращает `indexPath` из верхний или нижний колонтитул в разделе представления коллекции.
 - `GetSupplementaryView` – Возвращает заданный верхний или нижний колонтитул.
 - `GetVisibleSupplementaryViews` — Возвращает список всех видимых заголовка и нижние колонтитулы.
 - `UpdateInteractiveMovementTargetPosition` — Сообщает коллекции представление перемещено пользователем или перемещается элемент во время операции перетаскивания.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

Чтобы были внесены следующие изменения или дополнения `UICollectionViewController` класс в iOS 9:

 - `InstallsStandardGestureForInteractiveMovement` — Если `true` будет использоваться новый распознаватель жестов, автоматически поддерживает перетаскивание для изменения порядка.
 - `CanMoveItem` — Информирует представление коллекции, если данный элемент может быть перетащите перед началом записи.
 - `GetTargetContentOffset` — Используется для получения смещения элемент данной коллекции.
 - `GetTargetIndexPathForMove` — Возвращает `indexPath` данного элемента для операции перетаскивания.
 - `MoveItem` — Перемещает порядок данного элемента в списке.


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

Чтобы были внесены следующие изменения или дополнения `UICollectionViewDataSource` класс в iOS 9:

 - `CanMoveItem` — Информирует представление коллекции, если данный элемент может быть перетащите перед началом записи.
 - `MoveItem` — Перемещает порядок данного элемента в списке.

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

Чтобы были внесены следующие изменения или дополнения `UICollectionViewDelegate` класс в iOS 9:

 - `GetTargetContentOffset` — Используется для получения смещения элемент данной коллекции.
 - `GetTargetIndexPathForMove` — Возвращает `indexPath` данного элемента для операции перетаскивания.

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

Чтобы были внесены следующие изменения или дополнения `UICollectionViewFlowLayout` класс в iOS 9:

 - `SectionFootersPinToVisibleBounds` — Придерживается этого же разделе нижние колонтитулы в границы представления коллекции видимым.
 - `SectionHeadersPinToVisibleBounds` — Придерживается этого же заголовки разделов в границы представления коллекции видимым.

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

Чтобы были внесены следующие изменения или дополнения `UICollectionViewLayout` класс в iOS 9:

 - `GetInvalidationContextForEndingInteractiveMovementOfItems` — Возвращает контекст недействительности данных в конце операции перетаскивания, когда пользователь завершает Перетаскивание или отменяет ее.
 - `GetInvalidationContextForInteractivelyMovingItems` — Возвращает контекст недействительности данных в начале операции перетаскивания.
 - `GetLayoutAttributesForInteractivelyMovingItem` — Возвращает атрибуты разметки для данного элемента при перетаскивании элемента.
 - `GetTargetIndexPathForInteractivelyMovingItem` — Возвращает `indexPath` элемента, который находится в заданной точке при перетаскивании элемента.

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

Чтобы были внесены следующие изменения или дополнения `UICollectionViewLayoutAttributes` класс в iOS 9:

 - `CollisionBoundingPath` — Возвращает путь столкновений двух элементов при выполнении операции перетаскивания.
 - `CollisionBoundsType` — Возвращает тип конфликта (как `UIDynamicItemCollisionBoundsType`), произошло во время операции перетаскивания.

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

Чтобы были внесены следующие изменения или дополнения `UICollectionViewLayoutInvalidationContext` класс в iOS 9:

 - `InteractiveMovementTarget` — Возвращает конечный элемент операции перетаскивания.
 - `PreviousIndexPathsForInteractivelyMovingItems` — Возвращает `indexPaths` других элементов, участвующих в перетащите для изменения порядка операций.
 - `TargetIndexPathsForInteractivelyMovingItems` — Возвращает `indexPaths` элементов, которые изменят порядок в результате выполнения операции перетаскивания для изменения порядка.

### <a name="uicollectionviewsource"></a>UICollectionViewSource

Чтобы были внесены следующие изменения или дополнения `UICollectionViewSource` класс в iOS 9:

 - `CanMoveItem` — Информирует представление коллекции, если данный элемент может быть перетащите перед началом записи.
 - `GetTargetContentOffset` — Возвращает смещения, которые перемещаются с помощью операции перетаскивания для изменения порядка элементов.
 - `GetTargetIndexPathForMove` — Возвращает `indexPath` элемента, который будет перемещен во время операции перетаскивания для изменения порядка.
 - `MoveItem` — Перемещает порядок данного элемента в списке.

## <a name="summary"></a>Сводка

В этой статье рассматриваются изменения в представления коллекций в iOS 9 и описаны способы их реализации в Xamarin.iOS.
Здесь приводились реализации простого действия перетаскивания для изменения порядка в представлении коллекции; с помощью пользовательского распознавателя с помощью перетаскивания для возобновления; и перетащите для изменения порядка влияние макета представления пользовательской коллекции.

## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Пример представления коллекции](https://developer.xamarin.com/samples/monotouch/ios9/CollectionView/)
- [SimpleCollectionView (пример)](https://developer.xamarin.com/samples/SimpleCollectionView/)
- [События, протоколы и делегаты](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Работа с таблицами и ячейками](~/ios/user-interface/controls/tables/index.md)
