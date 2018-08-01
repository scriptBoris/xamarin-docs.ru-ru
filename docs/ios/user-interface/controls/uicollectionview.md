---
title: Представления коллекций в Xamarin.iOS
description: Представления коллекций разрешить доступ к содержимому будет отображаться с использованием произвольного макеты. Они позволяют легко компоновке вид сетки без дополнительной настройки, поддерживая пользовательских макетов.
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: b9ba2f885364084d6bee67c460b4831c00c7ae55
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790631"
---
# <a name="collection-views-in-xamarinios"></a>Представления коллекций в Xamarin.iOS

_Представления коллекций разрешить доступ к содержимому будет отображаться с использованием произвольного макеты. Они позволяют легко компоновке вид сетки без дополнительной настройки, поддерживая пользовательских макетов._

Представления коллекций, доступных в `UICollectionView` класса, являются новым понятием в iOS 6 представлен представление нескольких элементов на экране с помощью разметки. Шаблоны для предоставления данных для `UICollectionView` для создания элементов и взаимодействия с этих элементов, выполните же делегирование и источника данных шаблоны, обычно используемые при разработке операций ввода-вывода.

Тем не менее, представления коллекций работают с подсистемой макет, который не зависит от `UICollectionView` сам. Таким образом просто указав другой макет можно легко изменить представление представления коллекции.

iOS предоставляет класс макет с именем `UICollectionViewFlowLayout` , позволяющий макетов на основе строки, например сетки должен быть создан без дополнительных действий. Кроме того пользовательские макеты можно также создать, позволяющие любой презентации, которую можно себе представить.

## <a name="uicollectionview-basics"></a>Основы UICollectionView

`UICollectionView` Класс состоит из трех различных элементов:

-  **Ячейки** — управляемые данными представления для каждого элемента
-  **Дополнительные представления** — управляемые данными представлений, связанных с помощью раздела.
-  **Декорирование представления** — Non данными представлений, созданных макета

## <a name="cells"></a>Ячейки

Ячейки являются объекты, представляющие один элемент в наборе данных, которая содержится в представлении коллекции. Каждая ячейка представляет собой экземпляр `UICollectionViewCell` класс, который состоит из трех различных представлениях, как показано на рисунке ниже:

 [![](uicollectionview-images/01-uicollectionviewcell.png "Каждая ячейка состоит из трех различных представлениях, как показано ниже")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

`UICollectionViewCell` Класс имеет следующие свойства для каждого из этих представлений:

-   `ContentView` — Это представление содержит содержимое, которое представляет ячейку. Он отображается в верхнем z порядке на экране.
-   `SelectedBackgroundView` — Ячейки есть встроенная поддержка выбора. Это представление используется для визуального обозначения к ячейке. Оно отображается под `ContentView` при выборе ячейки.
-   `BackgroundView` — Ячейки можно также отобразить фон, представляемому `BackgroundView` . В этом представлении отображается под `SelectedBackgroundView` .


Установив `ContentView` таким образом, что оно становится меньше `BackgroundView` и `SelectedBackgroundView`, `BackgroundView` можно использовать для визуального фрейма содержимого, пока `SelectedBackgroundView` отображается при выборе ячейки, как показано ниже:

 [![](uicollectionview-images/02-cells.png "Элементы разных ячеек")](uicollectionview-images/02-cells.png#lightbox)

Ячейки, на снимке экрана выше создаются путем наследования от `UICollectionViewCell` и параметр `ContentView`, `SelectedBackgroundView` и `BackgroundView` свойства, соответственно, как показано в следующем коде:

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


## <a name="supplementary-views"></a>Дополнительные представления

Дополнительные представления — представления сведений, связанных с каждой части `UICollectionView`. Как и ячейки дополнительные представления, управляемых данными. Где ячейки представления элемента данных из источника данных, дополнительные представления представления данных раздела, например категории книги в полку или жанр музыки в библиотеке музыки.

Например дополнительные представления удалось использовать для предоставления заголовка для определенного раздела, как показано на рисунке ниже:

 [![](uicollectionview-images/02a-supplementary-view.png "Дополнительные представления позволяет представить заголовок для определенного раздела, как показано ниже")](uicollectionview-images/02a-supplementary-view.png#lightbox)

Чтобы использовать дополнительные представления, он сначала должен быть зарегистрирован в `ViewDidLoad` метод:

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

Затем необходимо получить с помощью представления `GetViewForSupplementaryElement`, созданный с помощью `DequeueReusableSupplementaryView`и наследует от `UICollectionReusableView`. Следующий фрагмент кода выдаст SupplementaryView, показанные на снимке экрана выше:

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

Дополнительные представления, более универсальными, чем просто верхние и нижние колонтитулы.
Они могут располагаться в любом месте в представлении коллекции и может состоять из любого представления, делая их внешний вид полностью настраиваемой.

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>Декорирование представления

Декорирование представления являются чисто визуальных представлений, которые могут быть отображены в `UICollectionView`. В отличие от ячеек и дополнительных представления они являются не управляемые данными. Они всегда создаются в подкласс макета и впоследствии можно изменять по мере макета содержимого. Например, параметризации представления может использоваться для представления фона представление, которое выполняет прокрутку содержимого `UICollectionView`, как показано ниже:

 [![](uicollectionview-images/02c-decoration-view.png "Декорирование представления с красным фоном")](uicollectionview-images/02c-decoration-view.png#lightbox)

 Приведенный далее фрагмент кода примет фона красным цветом образцы `CircleLayout` класса:

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

Как и в других частях операций ввода-вывода, таких как `UITableView` и `MKMapView`, `UICollectionView` получает данные от *источника данных*, предоставляемый в Xamarin.iOS через **`UICollectionViewDataSource`** класса. Этот класс отвечает за предоставление содержимого `UICollectionView` такие как:

-  **Ячейки** — возвращаемые `GetCell` метод.
-  **Дополнительные представления** — возвращаемые `GetViewForSupplementaryElement` метод.
-  **Число разделов** — возвращаемые `NumberOfSections` метод. По умолчанию-1, если не реализовано.
-  **Число элементов в разделе** — возвращаемые `GetItemsCount` метод.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
Для удобства `UICollectionViewController` класс доступен. Это настраивается автоматически для обоих делегат, который рассматривается в следующем разделе, и источник данных для его `UICollectionView` представления.

Как и в `UITableView`, `UICollectionView` класса будут вызывать только источника данных, чтобы получить ячейки для элементов на экране.
Ячейки, прокрутите вне экрана, помещаются в очередь для повторного использования, как показано на следующем рисунке:

 [![](uicollectionview-images/03-cell-reuse.png "Ячейки, прокрутите за пределы экрана помещаются в очередь для повторного использования, как показано ниже")](uicollectionview-images/03-cell-reuse.png#lightbox)

Повторное использование ячейки упрощена с `UICollectionView` и `UITableView`. Больше не требуется для создания ячейки непосредственно в источнике данных, если он не доступен в очереди повторного использования, как ячейки зарегистрированные в системе. Если ячейки не доступен при выполнении вызова для отмены очередь ячейки из очереди повторного использования, операций ввода-вывода будут созданы автоматически в зависимости от типа или nib, которая была зарегистрирована.
Тот же метод также доступна для дополнительных представлений.

Например, рассмотрим следующий код, который регистрирует `AnimalCell` класса:

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

Когда `UICollectionView` должен ячейку, так как его элемент отображается на экране, `UICollectionView` вызывает источника данных `GetCell` метод. Подобно тому как это работает с UITableView, этот метод отвечает за настройку ячейки из резервное копирование данных, что было бы `AnimalCell` в этом случае.

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

Вызов `DequeReusableCell` — где ячейки будут иметь либо отмены в очередь из очереди повторного использования или, если ячейка не доступные в очереди, созданные в зависимости от типа, зарегистрированных в вызове `CollectionView.RegisterClassForCell`.

В этом случае можно зарегистрировать `AnimalCell` класса, операций ввода-вывода будет создана новая `AnimalCell` внутренне и вернуть его вызов отменяется очередь ячейки будет установлено, после чего он использует изображение содержится в классе животных и возвращаются для отображения `UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>делегат

`UICollectionView` Класс использует делегат типа `UICollectionViewDelegate` для поддержки взаимодействия с содержимым в `UICollectionView`. Это позволяет управлять:

-  **Выбор ячейки** — определяет, если ячейка выбрана.
-  **Выделение ячеек** — определяет, если ячейки в настоящее время затрагиваемых.
-  **Меню ячейки** — меню, отображаемое в ячейке в ответ на жест long клавишу.


Как и в источнике данных, `UICollectionViewController` настроен по умолчанию считается делегат `UICollectionView`.

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>Выделение ячеек

При нажатии ячейки, ячейки переходы в выделенный состояние, и она еще не выбрана, пока пользователь отрывает пальцев из ячейки. Это позволяет временные изменения внешнего вида ячейки до фактического выбора. После выбора ячейки в `SelectedBackgroundView` отображается. На следующем рисунке показана выделенная состояние непосредственно перед Выбор выполняется:

 [![](uicollectionview-images/04-cell-highlight.png "На этом рисунке показано выделенный состояние непосредственно перед Выбор выполняется")](uicollectionview-images/04-cell-highlight.png#lightbox)

Для реализации выделение цветом, `ItemHighlighted` и `ItemUnhighlighted` методы `UICollectionViewDelegate` может использоваться. Например, следующий код будет применяться желтый фон `ContentView` при выделении ячейки на белом фоне при отмене выделенной, как показано на приведенном выше рисунке:

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

При включении возможности выбора по умолчанию в `UICollectionView`. Чтобы отключить выбор, необходимо переопределить `ShouldHighlightItem` и возвращают значение false, как показано ниже:

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

При отключении выделение процесс выбора ячейки также отключен. Кроме того, имеется также `ShouldSelectItem` метод, который управляет выбора напрямую, несмотря на то что если `ShouldHighlightItem` реализован и возвращает значение false, `ShouldSelectItem` не вызывается.

 `ShouldSelectItem` позволяет выбрать включить или выключить на основе элементов при `ShouldHighlightItem` не реализован. Она также позволяет выделение без выбора, если `ShouldHighlightItem` реализован и возвращает значение true при `ShouldSelectItem` возвращает значение false.

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>Меню ячейки

Каждая ячейка в `UICollectionView` способен отображать меню, которое позволяет вырезания, копирования и вставки для поддержки (необязательно). Создание меню "Правка" ячейки:

1.  Переопределить `ShouldShowMenu` и возвращают значение true, если элемент должен Показать меню.
1.  Переопределить `CanPerformAction` и возвращают true для каждого действия, которое можно выполнить и элемент, который будет иметь одно из Вырезать, копировать и вставить.
1.  Переопределить `PerformAction` для выполнения на редактирование, копирование операции вставки.


Следующий снимок экрана Открытие меню, при нажатии долго ячейки:

 [![](uicollectionview-images/04a-menu.png "На этом снимке экрана Открытие меню, при нажатии долго ячейки")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>Макет

`UICollectionView` поддерживает макет, которая позволяет располагать все его элементы, ячейки, дополнительные представления и представления Декорирование управлять независимо от `UICollectionView` сам.
С помощью системы макета, приложение может поддерживать макетов, таких как один вид сетки, мы видели в этой статье, а также предоставляют пользовательские макеты.

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>Основы

Макеты в `UICollectionView` определены в классе, который наследует от `UICollectionViewLayout`. Реализация макета отвечает за создание атрибутов макета для каждого элемента в `UICollectionView`. Существует два способа для создания макета:

-  Используется встроенная `UICollectionViewFlowLayout` .
-  Предоставить пользовательский макет путем наследования от `UICollectionViewLayout` .


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>Последовательное размещение

`UICollectionViewFlowLayout` Класс предоставляет макет, подходящий для упорядочивания содержимого в сетке ячеек, как видно на основе строки.

Чтобы использовать потоковый макет:

-  Создайте экземпляр класса `UICollectionViewFlowLayout` :


```csharp
var layout = new UICollectionViewFlowLayout ();
```

-  Передайте этот экземпляр в конструктор `UICollectionView` :


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

Это все, что требуется для отображения сведений в сетке. Кроме того, если изменить ориентацию, `UICollectionViewFlowLayout` обрабатывает изменение расположения содержимого соответствующим образом, как показано ниже:

 [![](uicollectionview-images/05-layout-orientation.png "Пример изменение ориентации")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>Раздел «отступ»

Для предоставления некоторое пространство вокруг `UIContentView`, имеют макеты `SectionInset` свойство типа `UIEdgeInsets`. Например, следующий код предоставляет буфер 50 пикселей вокруг каждого раздела `UIContentView` при заданном `UICollectionViewFlowLayout`:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

Это приводит к пробелы вокруг раздела, как показано ниже:

 [![](uicollectionview-images/06-sectioninset.png "Пробелы вокруг раздела, как показано ниже")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>Создание подклассов UICollectionViewFlowLayout

В выпуске с помощью `UICollectionViewFlowLayout` напрямую, он может также быть подклассом для дальнейшей настройки макета содержимого вдоль оси. Например это может использоваться для создания макета, не переносится в сетке ячеек, но вместо этого создает одну строку с горизонтальной прокруткой эффект, как показано ниже:

 [![](uicollectionview-images/07-line-layout.png "Одну строку с горизонтальной прокруткой эффекта")](uicollectionview-images/07-line-layout.png#lightbox)

Это можно реализовать путем создания подкласса `UICollectionViewFlowLayout` требуется:

-  Инициализация, любые свойства макета, применяемые к сам макет или все элементы в макете в конструктор.
-  Переопределение `ShouldInvalidateLayoutForBoundsChange` возвращается значение true, так что при границы `UICollectionView` пересчитывается изменения макет ячейки. Этот параметр используется в этом случае убедитесь что код для преобразования, примененного к ячейке centermost будут применены при прокрутке.
-  Переопределение `TargetContentOffset` вносить centermost ячейки привязки в центр `UICollectionView` как прокрутка останавливается.
-  Переопределение `LayoutAttributesForElementsInRect` для возвращения массива `UICollectionViewLayoutAttributes` . Каждый `UICollectionViewLayoutAttribute` содержит сведения о том, как макет конкретного элемента, включая свойства, такие как его `Center` , `Size` , `ZIndex` и `Transform3D` .


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

Помимо использования `UICollectionViewFlowLayout`, макеты можно также настраивать путем наследования непосредственно из `UICollectionViewLayout`.

Ниже приведены основные методы для переопределения.

-   `PrepareLayout` — Используется для выполнения начальной геометрические вычислений, которые будут использоваться на протяжении всего процесса макета.
-   `CollectionViewContentSize` — Возвращает размер области, используемой для отображения содержимого.
-   `LayoutAttributesForElementsInRect` — Как в приведенной выше примере UICollectionViewFlowLayout, этот метод используется для предоставления сведений о `UICollectionView` о том, как макет каждого элемента. Однако в отличие от `UICollectionViewFlowLayout` , при создании пользовательских макетов, однако можно выбрать можно расположить элементы.


Например это же содержимое быть представлены в циклические структуры, как показано ниже:

 [![](uicollectionview-images/08-circle-layout.png "Циклическая настраиваемый макет, как показано ниже")](uicollectionview-images/08-circle-layout.png#lightbox)

Мощный преимуществом макеты является то, что для изменения макета вид сетки на макете горизонтальной прокруткой и в циклических структуру требует предоставленный класс макета `UICollectionView` изменяться. Ничего в `UICollectionView`, его делегата или источника данных изменений кода вообще.


## <a name="changes-in-ios-9"></a>Изменения в iOS 9


В iOS 9, представление коллекции (`UICollectionView`) теперь поддерживает перетащите переупорядочения элементов без дополнительной настройки, добавив новый распознаватель жестов по умолчанию и новые вспомогательные методы.

Используя эти новые методы, можно легко реализовать с помощью перетаскивания изменить в представлении коллекции, а также настройка внешнего вида элементов любом этапе процесса изменения порядка.

[![](uicollectionview-images/intro01.png "Пример изменения порядка процесса")](uicollectionview-images/intro01.png#lightbox)

В этой статье мы ознакомитесь с реализации перетащите для изменения порядка в приложения Xamarin.iOS, а также некоторые изменения, сделанные в iOS 9 в элемент управления представления коллекции:

- [Легко переупорядочения элементов](#Easy-Reordering-of-Items)
    - [Простой пример изменения порядка](#Simple-Reordering-Example)
    - [С помощью пользовательского распознавателя](#Using-a-Custom-Gesture-Recognizer)
    - [Пользовательские макеты и изменение порядка](#Custom-Layouts-and-Reording)
- [Просмотр изменений коллекции](#Collection-View-Changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>Изменение порядка следования элементов

Как уже говорилось выше, одним из наиболее важных изменений в коллекции представление iOS 9 был добавления легко функциональных возможностей перетащите для изменения порядка без дополнительной настройки.

В iOS 9 — самый быстрый способ добавления, изменения порядка для представления коллекции для использования `UICollectionViewController`.
Контроллер представление коллекции теперь имеет `InstallsStandardGestureForInteractiveMovement` свойство, которое добавляет стандартный *распознаватель жестов* с поддержкой перетаскивания для изменения порядка элементов в коллекции.
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

В качестве примера быстрого запуска нового проекта Xamarin.iOS и изменить **Main.storyboard** файла. Перетащите `UICollectionViewController` на поверхность разработки:

[![](uicollectionview-images/quick01.png "Добавление UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

Выберите представление коллекции (он может быть проще всего это сделать на структуру документа). На вкладке «макет» панели свойств задайте следующие размеры, как показано на снимке экрана ниже:

- **Размер ячейки**: ширина – 60 | Высота — 60
- **Размер заголовка**: ширина – 0 | Высота – 0
- **Размер нижнего колонтитула**: ширина – 0 | Высота – 0
- **Интервал Min**: для ячейки — 8 | Строки – 8
- **Статьи отступы**: 16 верхних | Нижней — 16 | Left — 16 | Справа — 16

[![](uicollectionview-images/quick04.png "Задается размер представления коллекции")](uicollectionview-images/quick04.png#lightbox)

Измените значение по умолчанию ячейки:
    - Изменить цвет фона на синий
    - Добавить метку в качестве заголовка для ячейки
    - Значение идентификатора повторное использование **ячейки**

[![](uicollectionview-images/quick02.png "Изменения в ячейки по умолчанию")](uicollectionview-images/quick02.png#lightbox)

Добавление ограничений для поддержания метки по центру внутри ячейки, поскольку изменения размера:

В **Pad свойство** для _CollectionViewCell_ и задайте **класса** для `TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "Задайте класс TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

Задать **для повторного использования представления коллекции** для `Cell`:

[![](uicollectionview-images/quick06.png "Значение ячейки для повторного использования представления коллекции")](uicollectionview-images/quick06.png#lightbox)

Наконец, выберите метку и назовите его `TextLabel`:

[![](uicollectionview-images/quick07.png "Метка имени TextLabel")](uicollectionview-images/quick07.png#lightbox)

Изменить `TextCollectionViewCell` и добавьте следующие свойства.:

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

Здесь `Text` свойство метки указывается в виде заголовка ячейки, чтобы можно было задать из кода.

Добавьте в проект новый класс C# и назовите его `WaterfallCollectionSource`. Измените файл и сделать его выглядеть следующим образом:

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

Этот класс будет источником данных для наших представления коллекции и предоставляют сведения для каждой ячейки в коллекции.
Обратите внимание, что `MoveItem` позволяет перетаскивать элементы в коллекции, чтобы быть упорядоченный реализуется метод.

Добавьте еще один новый класс C# в проект и назовите его `WaterfallCollectionDelegate`. Измените этот файл и сделать его выглядеть следующим образом:

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

Будет использоваться в качестве делегата для наших представления коллекции. Методы были переопределены, чтобы выделить ячейку, как пользователь взаимодействует с ним в представлении коллекции.

Один последний класс C# добавьте в проект и назовите его `WaterfallCollectionView`. Измените этот файл и сделать его выглядеть следующим образом:

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

Обратите внимание, что `DataSource` и `Delegate` , созданным ранее задаются при создании представления коллекции из его раскадровки (или **.xib** файла).

Изменить **Main.storyboard** файл еще раз и выберите представление коллекции переключитесь **свойства**. Задать **класса** пользовательскому `WaterfallCollectionView` класс, который мы определенный выше:

Сохранить изменения, внесенные в пользовательский интерфейс и запустить приложение.
Если пользователь выбирает элемент в списке и перетаскивает его в новое расположение, другие элементы будут анимация автоматически при перемещении в сторону элемента.
Когда пользователь перетаскивает элемент в новое расположение, он сохранится в это расположение. Пример:

[![](uicollectionview-images/intro01.png "Примером при перетаскивании элемента в новое место")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>С помощью пользовательского распознавателя

В случаях, когда невозможно использовать `UICollectionViewController` и необходимо использовать обычный `UIViewController`, или если вы хотите выполнить больший контроль над жестов перетаскивания и вставки, можно создать свой собственный пользовательский распознаватель жестов и добавьте его в представлении коллекции при загрузке представления. Пример:

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

Здесь мы используем новые методы добавляются в представление коллекции для внедрения и управления операциями перетаскивания:

 - `BeginInteractiveMovementForItem` -Отмечает начало операции перемещения.
 - `UpdateInteractiveMovementTargetPosition` -Отправляется как обновить расположение этого элемента.
 - `EndInteractiveMovement` -Отмечает конец перемещения элемента.
 - `CancelInteractiveMovement` -Помечает пользователь отмену операции перемещения.

При запуске приложения, операции перетаскивания будет работать такие же значение по умолчанию перетащите распознаватель жестов, входящий в состав представления коллекции.

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>Пользовательские макеты и изменение порядка

В iOS 9 были добавлены новые методы для работы с перетащите для изменения порядка и пользовательских макетов в представлении коллекции. Чтобы ознакомиться с этой функцией, добавим в пользовательский макет в коллекцию.

Во-первых, добавьте новый класс C# называется `WaterfallCollectionLayout` в проект. Изменить его и сделать его выглядеть следующим образом:

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

Это может быть используется класс для предоставления пользовательского два столбца, каскадная макета типа в представлении коллекции.
В этом коде используется кодирование ключ-значение (через `WillChangeValue` и `DidChangeValue` методы) для обеспечения привязки данных для вычисляемого свойства в этом классе.

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

Далее следует изменить `WaterfallCollectionView` и добавьте следующее свойство вспомогательный:

```csharp
public WaterfallCollectionSource Source {
    get { return (WaterfallCollectionSource)DataSource; }
}
```

Это сделает упрощают получение в источник данных (и высота элемента) из пользовательского макета.

Наконец измените представление-контроллер и добавьте следующий код:

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

Это создает экземпляр наших пользовательских макетов, задает события можно указать размер каждого элемента и присоединяет новый макет для наших представления коллекции.

Если мы запустим приложение Xamarin.iOS представление коллекции теперь будет выглядеть следующим образом:

[![](uicollectionview-images/custom01.png "Представление коллекции теперь будет выглядеть следующим образом")](uicollectionview-images/custom01.png#lightbox)

Мы можем по-прежнему перетаскивания для переупорядочения элементы как и раньше, но теперь изменит размер в соответствии с их нового местоположения при перетаскивании их элементы.

## <a name="collection-view-changes"></a>Просмотр изменений коллекции

В следующих разделах мы сделаете подробное описание изменений, внесенных iOS 9 для каждого класса в представлении коллекции.

### <a name="uicollectionview"></a>UICollectionView

Чтобы были внесены следующие изменения или дополнения `UICollectionView` класс IOS 9:

 - `BeginInteractiveMovementForItem` — Отмечает начало операции перетаскивания.
 - `CancelInteractiveMovement` — Коллекция информирует представление, что пользователь отменил операцию перетаскивания.
 - `EndInteractiveMovement` — Коллекция информирует представление, что пользователь завершил операцию перетаскивания.
 - `GetIndexPathsForVisibleSupplementaryElements` — Возвращает `indexPath` из колонтитула в разделе представления коллекции.
 - `GetSupplementaryView` — Возвращает данной верхний или нижний колонтитул.
 - `GetVisibleSupplementaryViews` — Возвращает список всех отображается заголовок и нижний колонтитулы.
 - `UpdateInteractiveMovementTargetPosition` — Коллекция информирует представление перемещено пользователем или перемещается элемент во время операции перетаскивания.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

Чтобы были внесены следующие изменения или дополнения `UICollectionViewController` класса в iOS 9:

 - `InstallsStandardGestureForInteractiveMovement` — Если `true` распознаватель жестов, автоматически поддерживает перетащите для изменения порядка будет использоваться.
 - `CanMoveItem` — Сообщает представление коллекции, если данный элемент может быть переупорядочить перетаскивания.
 - `GetTargetContentOffset` — Используется для получения смещения элемент заданной коллекции.
 - `GetTargetIndexPathForMove` — Возвращает `indexPath` данного элемента для операции перетаскивания.
 - `MoveItem` — Перемещение порядок данного элемента в списке.


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

Чтобы были внесены следующие изменения или дополнения `UICollectionViewDataSource` класса в iOS 9:

 - `CanMoveItem` — Сообщает представление коллекции, если данный элемент может быть переупорядочить перетаскивания.
 - `MoveItem` — Перемещение порядок данного элемента в списке.

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

Чтобы были внесены следующие изменения или дополнения `UICollectionViewDelegate` класса в iOS 9:

 - `GetTargetContentOffset` — Используется для получения смещения элемент заданной коллекции.
 - `GetTargetIndexPathForMove` — Возвращает `indexPath` данного элемента для операции перетаскивания.

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

Чтобы были внесены следующие изменения или дополнения `UICollectionViewFlowLayout` класса в iOS 9:

 - `SectionFootersPinToVisibleBounds` — Элемент нижние колонтитулы раздел границ представление коллекции видимым.
 - `SectionHeadersPinToVisibleBounds` — Элемент заголовки границ представление коллекции видимым.

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

Чтобы были внесены следующие изменения или дополнения `UICollectionViewLayout` класса в iOS 9:

 - `GetInvalidationContextForEndingInteractiveMovementOfItems` — Возвращает контекст недействительности в конце операции перетаскивания, когда пользователь завершает Перетаскивание или отменяет его.
 - `GetInvalidationContextForInteractivelyMovingItems` — Возвращает контекст недействительности в начале операции перетаскивания.
 - `GetLayoutAttributesForInteractivelyMovingItem` — Возвращает атрибуты макета для данного элемента при перетаскивании элемента.
 - `GetTargetIndexPathForInteractivelyMovingItem` — Возвращает `indexPath` элемента, который находится в данный момент, при перетаскивании элемента.

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

Чтобы были внесены следующие изменения или дополнения `UICollectionViewLayoutAttributes` класса в iOS 9:

 - `CollisionBoundingPath` — Возвращает путь конфликт двух элементов во время операции перетаскивания.
 - `CollisionBoundsType` — Возвращает тип конфликта (как `UIDynamicItemCollisionBoundsType`) возникшее во время операции перетаскивания.

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

Чтобы были внесены следующие изменения или дополнения `UICollectionViewLayoutInvalidationContext` класса в iOS 9:

 - `InteractiveMovementTarget` — Возвращает целевой элемент операции перетаскивания.
 - `PreviousIndexPathsForInteractivelyMovingItems` — Возвращает `indexPaths` других элементов, участвующих в перетащите для изменения порядка операции.
 - `TargetIndexPathsForInteractivelyMovingItems` — Возвращает `indexPaths` элементов, которые изменят порядок в результате операции перетаскивания для изменения порядка.

### <a name="uicollectionviewsource"></a>UICollectionViewSource

Чтобы были внесены следующие изменения или дополнения `UICollectionViewSource` класса в iOS 9:

 - `CanMoveItem` — Сообщает представление коллекции, если данный элемент может быть переупорядочить перетаскивания.
 - `GetTargetContentOffset` — Возвращает смещения, перемещается через операции перетаскивания для изменения порядка элементов.
 - `GetTargetIndexPathForMove` — Возвращает `indexPath` элемента, который будет перемещен во время операции перетаскивания для изменения порядка.
 - `MoveItem` — Перемещение порядок данного элемента в списке.

## <a name="summary"></a>Сводка

В этой статье охваченных изменения представления коллекций в iOS 9 и описаны способы их реализации в Xamarin.iOS.
Он рассматривается реализация простого перетаскивания для возобновления действия в представлению коллекции; При помощи настраиваемый распознаватель жестов перетаскивания для переупорядочения; и как Перетащите для изменения порядка влияет на макет представления пользовательской коллекции.

## <a name="related-links"></a>Связанные ссылки

- [Образцы iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Пример представления коллекции](https://developer.xamarin.com/samples/monotouch/ios9/CollectionView/)
- [SimpleCollectionView (пример)](https://developer.xamarin.com/samples/SimpleCollectionView/)
- [События, протоколы и делегаты](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Работа с таблицами и ячеек](~/ios/user-interface/controls/tables/index.md)
