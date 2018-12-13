---
title: Общие сведения о шаблонах элементов управления Xamarin.Forms
description: Шаблоны элементов управления Xamarin.Forms дают возможность легко применять темы к страницам приложения и отменять их во время выполнения. В этой статье приводятся общие сведения о шаблонах элементов управления.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 6b7a6c6d9c9c541e1d5e821fc2dac202e98bec62
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994429"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Общие сведения о шаблонах элементов управления Xamarin.Forms

_Шаблоны элементов управления Xamarin.Forms дают возможность легко применять темы к страницам приложения и отменять их во время выполнения. В этой статье приводятся общие сведения о шаблонах элементов управления._

Элементы управления имеют свойства, определяющие различные аспекты их внешнего вида, например `BackgroundColor` и `TextColor`. Эти свойства можно задавать с помощью [стилей](~/xamarin-forms/user-interface/styles/index.md), которые можно изменять во время выполнения для базовой настройки темы. Однако стили не обеспечивают четкое разделение между внешним видом страницы и ее содержимым, и изменения, которые можно осуществлять путем задания таких свойств, ограничены.

Шаблоны элементов управления обеспечивают четкое разделение между внешним видом страницы и ее содержимым, что позволяет создавать страницы, к которым можно легко применять различные темы. Например, приложение может содержать шаблоны элементов управления на уровне приложения, которые определяют темную и светлую темы. Тема каждой страницы [`ContentPage`](xref:Xamarin.Forms.ContentPage) в приложении может определяться путем применения одного из шаблонов элементов управления без изменения содержимого страницы. Кроме того, темы, обеспечиваемые шаблонами элементов управления, не ограничиваются изменением свойств элементов управления. Они также позволяют изменять элементы управления, используемые для реализации темы.

## <a name="creating-a-controltemplate"></a>Создание шаблона ControlTemplate

Шаблон [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) определяет внешний вид страницы или представления и содержит корневой макет, который включает в себя элементы управления, реализующие шаблон. Как правило, шаблон `ControlTemplate` использует объект [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) для определения того, где будет отображаться содержимое страницы или представления. Затем страница или представление, которые используют `ControlTemplate`, определяют содержимое, отображаемое объектом `ContentPresenter`. На следующей схеме показан шаблон `ControlTemplate` для страницы, содержащий несколько элементов управления, в том числе элемент `ContentPresenter`, обозначенный синим прямоугольником:

![](introduction-images/control-template.png "Шаблон элемента управления для страницы")

Шаблон [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) можно применять к следующим типам путем задания их свойств `ControlTemplate`:

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

Когда шаблон [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) создается и назначается этим типам, текущий внешний вид заменяется внешним видом, определенным в `ControlTemplate`. Помимо настройки внешнего вида с помощью свойства `ControlTemplate`, шаблоны элементов управления можно применять с помощью стилей, что расширяет возможности тем.

> [!NOTE]
>  *Поясним, что представляют собой типы `TemplatedPage` и `TemplatedView`.* `TemplatedPage` — это базовый класс для `ContentPage`. Это самый общий тип страницы в Xamarin.Forms. В отличие от `ContentPage` класс `TemplatedPage` не имеет свойства `Content`. Поэтому добавлять содержимое в экземпляр `TemplatedPage` напрямую невозможно. Вместо этого содержимое добавляется путем задания шаблона элемента управления для экземпляра `TemplatedPage`. Аналогично `TemplatedView` — это базовый класс для `ContentView`. В отличие от `ContentView` класс `TemplatedView` не имеет свойства `Content`. Поэтому добавлять содержимое в экземпляр `TemplatedView` напрямую невозможно. Вместо этого содержимое добавляется путем задания шаблона элемента управления для экземпляра `TemplatedView`.

Шаблоны элементов управления можно создавать на XAML и C#.

- Шаблоны элементов управления, создаваемые на XAML, определяются в словаре [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), который присваивается коллекции [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) страницы или чаще коллекции [`Resources`](xref:Xamarin.Forms.Application.Resources) приложения.
- Шаблоны элементов управления, создаваемые на C#, обычно определяются в классе страницы или в классе с глобальным доступом.

От того, где определен экземпляр [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate), зависит то, где его можно использовать.

- Экземпляры [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate), определенные на уровне страницы, можно применять только к странице.
- Экземпляры [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate), определенные на уровне приложения, можно применять к любым страницам приложения.

Шаблоны элементов управления, которые находятся ниже в иерархии представлений, имеют приоритет над определенными выше в иерархии. Например, шаблон [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) с именем `DarkTheme`, определенный на уровне страницы, имеет приоритет над шаблоном с таким же именем, определенным на уровне приложения. Поэтому шаблон элемента управления, определяющий тему, которая применяется к каждой странице приложения, должен определяться на уровне приложения.


## <a name="related-links"></a>Связанные ссылки

- [Стили](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
