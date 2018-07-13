---
title: Сводка Глава 27. Пользовательские модули подготовки отчетов
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 27. Пользовательские модули подготовки отчетов'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 0497770909b33108eaac0fa5044e98febeb61763
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996312"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Сводка Глава 27. Пользовательские модули подготовки отчетов

Элемент Xamarin.Forms, такие как `Button` визуализируется с помощью кнопки с платформой, инкапсулируется в класс с именем `ButtonRenderer`.  Вот [версию iOS `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), [версии Android `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)и [версии среды выполнения Windows `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ButtonRenderer.cs).

В этой главе рассматривается, как можно написать собственные модули подготовки отчетов для создания пользовательских представлений, которые сопоставляются с объектами платформы.

## <a name="the-complete-class-hierarchy"></a>Иерархии полного класса

Существует семь сборок, содержащих код платформы Xamarin.Forms.
Вы можете просмотреть источник на GitHub, используя следующие ссылки:

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (очень небольшого размера)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.WinRT** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT) (больше следующие три)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)
- [**Xamarin.Forms.Platform.WinRT.Tablet**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Tablet)
- [**Xamarin.Forms.Platform.WinRT.Phone**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Phone)

[ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) пример отображает иерархию классов для сборок, которые являются допустимыми для выполнения платформы.

Вы заметите важный класс с именем `ViewRenderer`. Это класс, производных от при создании в модуль подготовки отчетов для конкретной платформы. Такой объект содержится в трех различных версий, так как он привязан к системе представление целевой платформы:

IOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L26) имеет универсальных аргументов:

- `TView` ограничен [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` ограничен [`UIKit.UIView`](https://developer.xamarin.com/api/type/UIKit.UIView/)

Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L14) имеет универсальных аргументов:

- `TView` ограничен [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` ограничен [`Android.Views.View`](https://developer.xamarin.com/api/type/Android.Views.View/)

Среда выполнения Windows [ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ViewRenderer.cs#L12) по-разному с именем универсальных аргументов:

- `TElement` ограничен [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` ограничен [`Windows.UI.Xaml.FrameworkElement`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.aspx)

При записи в модуль подготовки отчетов, будет производным от класса `View`и написав несколько `ViewRenderer` классов, по одному для каждой поддерживаемой платформы. Каждая реализация платформы будет ссылаться на собственный класс, производный от типа, который указан в качестве `TNativeView` или `TNativeElement` параметра.

## <a name="hello-custom-renderers"></a>Привет, пользовательские отрисовщики!

[ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) программа ссылается на пользовательское представление с именем `HelloView` в его [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) класса.

[ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) Класс включен в **HelloRenderers** проекта и просто является производным от `View`.

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) В класс **HelloRenderers.iOS** проекта является производным от `ViewRenderer<HelloView, UILabel>`. В `OnElementChanged` переопределение, он создает собственный iOS `UILabel` и вызывает метод `SetNativeControl`.

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) В класс **HelloRenderers.Droid** проекта является производным от `ViewRenderer<HelloView, TextView>`. В `OnElementChanged` переопределение, он создает Android `TextView` и вызывает метод `SetNativeControl`.

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) В класс **HelloRenderers.UWP** и другие проекты Windows является производным от `ViewRenderer<HelloView, TextBlock>`. В `OnElementChanged` переопределение, он создает Windows `TextBlock` и вызывает метод `SetNativeControl`.

Все `ViewRenderer` содержат производные `ExportRenderer` атрибут на уровне сборки, которая связывает `HelloView` класс с определенной `HelloViewRenderer` класса. Это обнаружение модулей подготовки отчетов в проекты для отдельных платформ Xamarin.Forms:

[![Тройной снимок экрана Hello представления](images/ch27fg02-small.png "пользовательские Отрисовщики")](images/ch27fg02-large.png#lightbox "пользовательские Отрисовщики")

## <a name="renderers-and-properties"></a>Модули подготовки отчетов и свойства

Следующий набор модулей подготовки отчетов реализует Рисование эллипса и находится в различных проектов из [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) решения.

[ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) Класс находится в **Xamarin.FormsBook.Platform** платформы. Аналогичен классу `BoxView` и определяет только одно свойство: `Color` типа `Color`.

Модули подготовки отчетов могут передавать значения свойств, заданные на `View` собственный объект путем переопределения `OnElementPropertyChanged` метод в модуль подготовки отчетов. В этом методе (и в большую часть модуля подготовки отчетов) доступны два свойства:

- `Element`, элемент Xamarin.Forms
- `Control`, собственного представления или объект мини-приложение или элемент управления

Типы эти свойства определяются универсальных параметров `ViewRenderer`. В этом примере `Element` имеет тип `EllipseView`.

`OnElementPropertyChanged` Переопределение таким образом можно передать `Color` значение `Element` собственному `Control` объекта, возможно, с каким-либо преобразования. Приведены три модулей подготовки отчетов.

- iOS: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), которая использует [ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) класс эллипса.
- Android — [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), которая использует [ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) класс эллипса.
- Среда выполнения Windows: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), который можно использовать собственный Windows [ `Ellipse` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.ellipse.aspx) класса.

[ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) класс отображает некоторые из них `EllipseView` объектов:

[![Тройной снимок экрана: демонстрация эллипс](images/ch27fg03-small.png "EllipseView пользовательские Отрисовщики")](images/ch27fg03-large.png#lightbox "EllipseView пользовательские Отрисовщики")

[ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) подпрыгивает `EllipseView` off сторон экрана.

## <a name="renderers-and-events"></a>Модули подготовки отчетов и событий

Можно также для модулей подготовки отчетов, косвенно создать события. [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) Класс похож на обычный Xamarin.Forms `Slider` , но позволяет указать ряд дискретных действий между `Minimum` и `Maximum` значения.

Приведены три модулей подготовки отчетов.

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- Среда выполнения Windows: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Визуализаторов обнаруживать изменения в собственный элемент управления, а затем вызвать `SetValueFromRenderer`, которая ссылается на свойство, используемое, определенные в `StepSlider`, изменение чего `StepSlider` срабатывание `ValueChanged` событий.

[ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) в нем демонстрируется этот новый ползунок.



## <a name="related-links"></a>Связанные ссылки

- [Глава 27 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Глава 27-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Пользовательские отрисовщики](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
