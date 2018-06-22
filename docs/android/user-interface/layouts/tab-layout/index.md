---
title: Макеты с вкладками
description: Общие сведения о макетах с вкладками в Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/08/2017
ms.openlocfilehash: 53ed5f91583d43839e96388194aea8c0d6ac5315
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34149264"
---
# <a name="tabbed-layouts"></a>Макеты с вкладками


## <a name="overview"></a>Обзор

Вкладки — шаблон распространенных пользовательских интерфейса в мобильных приложениях из-за их простоту и удобство использования. Они обеспечивают согласованность и простой способ перехода между экранов в приложении. Android имеет несколько API-интерфейса для интерфейсов с вкладками. 

-   **Панели действий** &ndash; входит новый набор API-интерфейса, появившихся в версии 3.0 Android (API уровня 11) с целью предоставления согласованного навигации и переключение представлений интерфейса. Которое было обратно перенесено в ОС Android 2.2 (API уровня 8) с [библиотеку поддержки Android версии 7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

-   **PagerTabStrip** &ndash; показывает current, следующей и предыдущей страницы `ViewPager`. `ViewPager` она доступна только через [библиотеку поддержки Android версии 4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Дополнительные сведения о `PagerTabStrip`, в разделе [ViewPager](~/android/user-interface/controls/view-pager/index.md).

-   **Панель инструментов** &ndash; `Toolbar` является компонентом панели действий, новая и более гибкие, который заменяет `ActionBar`. `Toolbar` доступна в Android без описания операций 5.0 или более поздней версии, и она доступна в старых версиях Android через [библиотеку поддержки Android версии 7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) пакет NuGet. 
    `Toolbar` сейчас компонент полосы рекомендуемые действия для использования в приложениях для Android.
    Дополнительные сведения см. в разделе [инструментов](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="related-links"></a>Связанные ссылки

- [Вкладки материала конструктора -](https://material.io/guidelines/components/tabs.html)- [панели действий](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Пакет NuGet AppCompat v7 библиотеку поддержки Android](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [Библиотека v7 совместимости приложений](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
