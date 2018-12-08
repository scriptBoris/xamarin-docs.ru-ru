---
title: Макеты Xamarin.Forms
description: Макеты Xamarin.Forms используются для создания элементов управления пользовательского интерфейса в visual структуры. В этой статье перечислены макеты, включенные в Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 520a90030200a1371b15331868ec6e325c6d3694
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053335"
---
# <a name="xamarinforms-layouts"></a>Макеты Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/FormsGallery/)

_Макеты Xamarin.Forms используются для создания элементов управления пользовательского интерфейса в visual структуры._

[ `Layout` ](xref:Xamarin.Forms.Layout) И [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) классы в Xamarin.Forms — специализированные подтипы представлений, которые являются контейнерами для представлений и других значений. `Layout` Класс сам является производным от [ `View` ](views.md). Объект `Layout` производных обычно содержит логику, чтобы задать положение и размер дочерних элементов в приложениях Xamarin.Forms.

[![Типы макета Xamarin.Forms](layouts-images/layouts-sml.png "типы макета Xamarin.Forms")](layouts-images/layouts.png#lightbox "типы макета Xamarin.Forms")

Классы, производные от `Layout` можно разделить на две категории:

## <a name="layouts-with-single-content"></a>Макеты с одиночное содержимое

Эти классы являются производными от [ `Layout` ](xref:Xamarin.Forms.Layout), который определяет [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) и [ `IsClippedToBounds` ](xref:Xamarin.Forms.Layout.IsClippedToBounds) свойства.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView) содержит один дочерний элемент, в котором заданы [ `Content` ](xref:Xamarin.Forms.ContentView.Content) свойство. `Content` Свойство может устанавливаться к любому `View` производных продуктов, включая другие `Layout` производные от него. `ContentView` обычно используется как элемент структурного и служит в качестве базового класса для [ `Frame` ](#frame).<br /><br />[Документация по API](xref:Xamarin.Forms.ContentView) | [![Пример ContentView](layouts-images/ContentView.png "пример ContentView")](layouts-images/ContentView-Large.png#lightbox "ContentView пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Frame

|     |     |
| --- | --- |
| [ `Frame` ](xref:Xamarin.Forms.Frame) Класс является производным от [ `ContentView` ](#contentView) и отображает прямоугольную рамку вокруг его дочерних. `Frame` имеет значение по умолчанию [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) значение 20, а также определяет [ `OutlineColor` ](xref:Xamarin.Forms.Frame.OutlineColor), [ `CornerRadius` ](xref:Xamarin.Forms.Frame.CornerRadius), и [ `HasShadow` ](xref:Xamarin.Forms.Frame.HasShadow)свойства.<br /><br />[Документация по API](xref:Xamarin.Forms.Frame) | [![Frame пример](layouts-images/Frame.png "кадров пример")](layouts-images/Frame-Large.png#lightbox "кадров пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView) возможна прокрутка его содержимое. Задайте [ `Content` ](xref:Xamarin.Forms.ScrollView.Content) свойство к представлению или макета слишком большой помещаются на экране. (Содержимое `ScrollView` очень часто [ `StackLayout` ](#stackLayout).) Задайте [ `Orientation` ](xref:Xamarin.Forms.ScrollView.Orientation) свойство для указания если прокрутка по вертикали, горизонтали или оба.<br /><br />[Документация по API](xref:Xamarin.Forms.ScrollView) / [руководство](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [образца](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Пример ScrollView](layouts-images/ScrollView.png "пример ScrollView")](layouts-images/ScrollView-Large.png#lightbox "пример ScrollView")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) Отображает содержимое с помощью шаблона элемента управления, и является базовым классом для [ `ContentView` ](#contentView).<br /><br />[Документация по API](xref:Xamarin.Forms.TemplatedView) / [руководство](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Пример TemplatedView](layouts-images/TemplatedView.png "пример TemplatedView")](layouts-images/TemplatedView.png#lightbox "TemplatedView пример") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) является руководителем макета для шаблонного представлений, используемых в [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) для пометки, где отображается содержимое, должна присутствовать.<br /><br />[Документация по API](xref:Xamarin.Forms.ContentPresenter) / [руководство](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Пример ContentPresenter](layouts-images/ContentPresenter.png "пример ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "пример ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Макеты с нескольких дочерних элементов

Эти классы являются производными от [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout) размещает дочерние элементы в стеке, либо горизонтально или вертикально на основании [ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) свойство. [ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing) Свойство определяет расстояние между дочерние элементы и имеет значение по умолчанию 6.<br /><br />[Документация по API](xref:Xamarin.Forms.StackLayout) / [руководство](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [образца](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)| [![Пример StackLayout](layouts-images/StackLayout.png "пример StackLayout")](layouts-images/StackLayout-Large.png#lightbox "пример StackLayout")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grid

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid) помещает его дочерних элементов в сетке из строк и столбцов. Положение дочернего элемента указывается с помощью [присоединенных свойств](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](xref:Xamarin.Forms.Grid.RowProperty), [ `Column` ](xref:Xamarin.Forms.Grid.ColumnProperty), [ `RowSpan` ](xref:Xamarin.Forms.Grid.RowSpanProperty), и [ `ColumnSpan` ](xref:Xamarin.Forms.Grid.ColumnSpanProperty).<br /><br />[Документация по API](xref:Xamarin.Forms.Grid) / [руководство](~/xamarin-forms/user-interface/layouts/grid.md) / [образца](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Пример сетки](layouts-images/Grid.png "пример сетки")](layouts-images/Grid-Large.png#lightbox "пример сетки")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) размещает дочерние элементы в определенные расположения относительно его родительского элемента. Положение дочернего элемента указывается с помощью [присоединенных свойств](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) и [ `LayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty). `AbsoluteLayout` Удобно для анимации положения представления.<br /><br />[Документация по API](xref:Xamarin.Forms.AbsoluteLayout) / [руководство](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [образца](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Пример AbsoluteLayout](layouts-images/AbsoluteLayout.png "пример AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "AbsoluteLayout пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) с [кода](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) размещает дочерние элементы относительно `RelativeLayout` себя или своих одноуровневых элементов. Положение дочернего элемента указывается с помощью [присоединенных свойств](~/xamarin-forms/xaml/attached-properties.md) , которые задаются для объектов типа [ `Constraint` ](xref:Xamarin.Forms.Constraint) и [ `BoundsConstraint` ](xref:Xamarin.Forms.Constraint).<br /><br />[Документация по API](xref:Xamarin.Forms.RelativeLayout) / [руководство](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [образца](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Пример RelativeLayout](layouts-images/RelativeLayout.png "пример RelativeLayout")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) основан на CSS [гибкий модуль макета рамок](http://www.w3.org/TR/css-flexbox-1/), известный как _гибкий макет_ или _flex-box_. `FlexLayout` Определяет шесть привязываемые свойства и пять вложенных привязываемые свойства, которые позволяют дочерним элементам с накоплением и оболочку с многие параметры выравнивания и ориентацию.<br /><br />[Документация по API](xref:Xamarin.Forms.FlexLayout) / [руководство](~/xamarin-forms/user-interface/layouts/flex-layout.md) / [образца](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/) | [![Пример FlexLayout](layouts-images/FlexLayout.png "пример FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "FlexLayout пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Связанные ссылки

- [Введение в Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Пример Xamarin.Forms FormsGallery](https://developer.xamarin.com/samples/FormsGallery/)
- [Примеры Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Документация по API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
