---
title: Общие сведения о шаблонах элементов управления Xamarin.Forms
description: Шаблоны элементов управления Xamarin.Forms дают возможность легко темой и re темой страницы приложений во время выполнения. В этой статье содержатся общие сведения о шаблонах элементов управления.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 6b7a6c6d9c9c541e1d5e821fc2dac202e98bec62
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994429"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Общие сведения о шаблонах элементов управления Xamarin.Forms

_Шаблоны элементов управления Xamarin.Forms дают возможность легко темой и re темой страницы приложений во время выполнения. В этой статье содержатся общие сведения о шаблонах элементов управления._

Элементы управления имеют разные свойства, такие как `BackgroundColor` и `TextColor`, который можно определить внешний вид элемента управления. Эти свойства можно задать с помощью [стили](~/xamarin-forms/user-interface/styles/index.md), который может быть изменен во время выполнения для реализации основных темы. Тем не менее стили не поддерживать четкое разделение между внешний вид страницы и его содержимого и изменения, которые можно сделать, задав такие свойства ограничены.

Шаблоны элементов управления дают четкое разделение между внешний вид страницы и его содержимым, таким образом, что позволяет создавать страницы, которые можно легко применять различные темы. Например приложение может содержать шаблоны управления уровня приложения, которые обеспечивают темной и светлой темами. Каждый [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) в приложении можно применять различные темы, применив один из шаблонов элементов управления без изменения содержимого, отображаемого для каждой страницы. Кроме того темы, предоставляемые шаблоны элементов управления не только для изменения свойств элементов управления. Они также можно изменить элементы управления, используемые для реализации темы.

## <a name="creating-a-controltemplate"></a>Создание шаблона ControlTemplate

Объект [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) указывает внешний вид страницы или представления и содержит корневой макет и в макете, элементы управления, реализующих шаблон. Как правило `ControlTemplate` будет использовать [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) для пометки, где будет отображаться содержимое, отображаемое на странице или представлении. Страницу или представление, использующее `ControlTemplate` этого необходимо определить содержимое, отображаемое по `ContentPresenter`. На следующей схеме показана `ControlTemplate` для страницы, которая содержит несколько элементов управления, включая `ContentPresenter` обозначен синий прямоугольник:

![](introduction-images/control-template.png "Шаблон элемента управления для страницы")

Объект [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) могут применяться к следующим типам, задав их `ControlTemplate` свойства:

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

Когда [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) создается и назначается на эти типы, любой существующий внешний вид заменяется внешний вид, определяемый в `ControlTemplate`. Кроме того, а также настройка внешнего вида с помощью `ControlTemplate` свойства, шаблоны могут применяться также с помощью стилей для дальнейшего управления разверните возможности темы.

> [!NOTE]
>  *Что такое `TemplatedPage` и `TemplatedView` типов?* `TemplatedPage` является базовым классом для `ContentPage`это самый простой тип страницы, предоставляемые Xamarin.Forms. В отличие от `ContentPage`, `TemplatedPage` имеет `Content` свойства. Таким образом, содержимое невозможно непосредственно добавлять к `TemplatedPage` экземпляра. Вместо этого содержимое добавляется, задав шаблон элемента управления для `TemplatedPage` экземпляра. Аналогичным образом `TemplatedView` является базовым классом для `ContentView`. В отличие от `ContentView`, `TemplatedView` имеет `Content` свойства. Таким образом, содержимое невозможно непосредственно добавлять к `TemplatedView` экземпляра. Вместо этого содержимое добавляется, задав шаблон элемента управления для `TemplatedView` экземпляра.

Шаблоны элементов управления могут создаваться в XAML и C#:

- Шаблоны элементов управления, созданные в XAML, определяются в [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) , назначенный [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) коллекции, или чаще для [ `Resources` ](xref:Xamarin.Forms.Application.Resources) коллекции приложения.
- Шаблоны элементов управления, созданном на C# обычно определяются в классе страницы или в классе, который может осуществляться глобально.

Выбор места для определения [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) экземпляра последствий, где его можно использовать:

- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) экземпляры, определенные на уровне страниц может применяться только к странице.
- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) экземпляры, определенные на уровне приложения могут применяться к страницам в приложении.

Шаблоны элементов управления, более низкого уровня в иерархии представлений имеют приоритет над определенных выше вверх. Например [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) с именем `DarkTheme` , определяется на уровне страницы будет иметь приоритет над шаблоном с одинаковыми именами, определенные на уровне приложения. Таким образом шаблон элемента управления, который определяет тему для применения к каждой страницы в приложении должен быть определен на уровне приложения.


## <a name="related-links"></a>Связанные ссылки

- [Стили](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
