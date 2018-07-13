---
title: Xamarin.Forms навигации
description: В этом руководстве объясняется, как выполнить переход в приложения Xamarin.Forms. Xamarin.Forms предоставляет ряд различных способов перехода по страницам в зависимости от используемого типа страницы.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 202f044ebd7dd5b110b94d2aa60eeb7151150607
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994732"
---
# <a name="xamarinforms-navigation"></a>Xamarin.Forms навигации

_Xamarin.Forms предоставляет ряд различных способов перехода по страницам в зависимости от используемого типа страницы._

![](images/page-types.png "Типы страниц Xamarin.Forms")

## <a name="hierarchical-navigationhierarchicalmd"></a>[Иерархическая навигация](hierarchical.md)

Класс [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) обеспечивает иерархическую навигацию, при которой пользователь может переходить по страницам вперед и назад по своему желанию. Этот класс реализует навигацию на основе стека объектов [`Page`](xref:Xamarin.Forms.Page) по методу LIFO (последним поступил — первым обслужен).

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

Xamarin.Forms [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) состоит из списка вкладок и область сведений, большего размера, причем каждая вкладка загрузке содержимого в области сведений.

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) — это страница, на которой пользователи могут проведите пальцем в сторону перемещаться по страницам содержимого, например коллекции.

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

Xamarin.Forms [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) — это страница, которая управляет связанных данных — две страницы, главной страницы, которая представляет элементы и страницу сведений, в которой представлены сведения об элементах на главной странице.

## <a name="modal-pagesmodalmd"></a>[Модальные страницы](modal.md)

Xamarin.Forms также поддерживает модальные страницы. На модальной странице пользователь должен выполнить отдельную задачу, причем он не может уйти с этой страницы, пока задача не будет выполнена или отменена.

## <a name="displaying-pop-upspop-upsmd"></a>[Отображение всплывающих элементов](pop-ups.md)

Xamarin.Forms предоставляет два во всплывающем регистрации подобные элементы интерфейса: предупреждение и листом действие. Эти элементы интерфейса можно использовать простые вопросы пользователей и помогает пользователям сделать задачи.
