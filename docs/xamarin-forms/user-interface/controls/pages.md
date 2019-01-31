---
title: Страницы Xamarin.Forms
description: Страницы Xamarin.Forms представляют экраны кросс платформенных мобильных приложений. В этой статье перечислены страницы, которые включены в Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: ce10a2e6ac91f67f8b4f664c232de2a3c97a5ba6
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292003"
---
# <a name="xamarinforms-pages"></a>Страницы Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/FormsGallery/)

_Страницы Xamarin.Forms представляют экраны кросс платформенных мобильных приложений._

Все типы страниц, которые описаны ниже, являются производными от Xamarin.Forms [ `Page` ](xref:Xamarin.Forms.Page) класса. Эти визуальные элементы занимают всего или большей части экрана. Объект `Page` представляет объект `ViewController` в iOS и `Page` в универсальной платформы Windows. В Android, каждая страница занимает экран, похожий `Activity`, но страницы Xamarin.Forms являются *не* `Activity` объектов.

[ ![](pages-images/pages-sml.png "Типы страницы Xamarin.Forms")](pages-images/pages.png#lightbox "типы страницы Xamarin.Forms")

## <a name="pages"></a>Pages

Xamarin.Forms поддерживает следующие типы страниц:

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage) — Это самый простой и наиболее распространенный тип страницы. Задайте [ `Content` ](xref:Xamarin.Forms.ContentPage.Content) свойство к одному [ `View` ](views.md) объект, который чаще всего [ `Layout` ](layouts.md) например [ `StackLayout` ](layouts.md#stackLayout), [ `Grid` ](layouts.md#grid), или [ `ScrollView` ](layouts.md#scrollView).<br /><br />[Документация по API](xref:Xamarin.Forms.ContentPage) | [![Пример ContentPage](pages-images/ContentPage.png "пример ContentPage")](pages-images/ContentPage-Large.png#lightbox "пример ContentPage")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Объект [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) управляет двух областей данных. Задайте [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) свойство на страницу, обычно отображается в списке или меню. Задайте [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) свойства страница со списком выбранный элемент из главной страницы. [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) Регулирует ли страница основной или подробности.<br /><br />[Документация по API](xref:Xamarin.Forms.MasterDetailPage) / [руководство](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [образца](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/) | [![Пример MasterDetailPage](pages-images/MasterDetailPage.png "пример MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) с [кода](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) Управляет перемещением других страниц, используя архитектуру на основе стека. При использовании Навигация по страницам в приложении, домашней страницы экземпляра должно передаваться конструктору `NavigationPage` объекта.<br /><br />[Документация по API](xref:Xamarin.Forms.NavigationPage) / [руководство](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [пример 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/), [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/), и [3](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)  | [![Пример NavigationPage](pages-images/NavigationPage.png "пример NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) с [код = за](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) является производным от абстрактного [ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1) класса и позволяет навигацию между дочерние страницы, с помощью вкладок. Задайте [ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children) свойство в коллекцию страниц или набора [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) свойство в коллекцию объектов данных и [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) свойства [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) описывающее как визуально представить каждый объект.<br /><br />[Документация по API](xref:Xamarin.Forms.TabbedPage) / [руководство](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [пример 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/) и [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage) | [![Пример TabbedPage](pages-images/TabbedPage.png "пример TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) является производным от абстрактного [ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1) класса и позволяет навигацию между дочерние страницы посредством считывания палец. Задайте [ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children) свойство в коллекцию [ `ContentPage` ](#contentPage) объектов или набор [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) свойство в коллекцию объектов данных и [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) свойства [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) описывающее как визуально представить каждый объект.<br /><br />[Документация по API](xref:Xamarin.Forms.CarouselPage) / [руководство](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [пример 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/) и [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/) | [![Пример CarouselPage](pages-images/CarouselPage.png "пример CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage пример")<br />[Код C# для этой страницы](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [страницы XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) Отображает содержимое весь экран с помощью шаблона элемента управления, и является базовым классом для [ `ContentPage` ](#contentPage).<br /><br />[Документация по API](xref:Xamarin.Forms.TemplatedPage) / [руководство](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Пример TemplatedPage](pages-images/TemplatedPage.png "пример TemplatedPage")](pages-images/TemplatedPage.png "TemplatedPage пример") |
|     |     |

## <a name="related-links"></a>Связанные ссылки

- [Пример Xamarin.Forms FormsGallery](https://developer.xamarin.com/samples/FormsGallery/)
- [Примеры Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Документация по API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
