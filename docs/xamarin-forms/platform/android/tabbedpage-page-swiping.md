---
title: Страница TabbedPage, проведение пальцем по экрану в Android
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать Android конкретных платформ, позволяющий считывания с жест палец по горизонтали между страницами в TabbedPage.
ms.prod: xamarin
ms.assetid: D1C09CCB-7246-41A4-8BD2-FA6FABCF1C72
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 7de2c903da40c186560085d61b94fd38ffe9d219
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209489"
---
# <a name="tabbedpage-page-swiping-on-android"></a>Страница TabbedPage, проведение пальцем по экрану в Android

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Это Android платформы используется для включения считывания с жест палец по горизонтали между страницами в [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Он используется в XAML, задав [ `TabbedPage.IsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) вложенное свойство, чтобы `boolean` значение:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

`TabbedPage.On<Android>` Метод указывает, что этой платформы будет выполняться только в Android. [ `TabbedPage.SetIsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled(Xamarin.Forms.BindableObject,System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) пространства имен, используется для включения считывания между страницами в [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Кроме того `TabbedPage` в класс `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` пространство имен также содержит [ `EnableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) метод, позволяющий этой платформой, и [ `DisableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) метод, который отключает этой платформы. [ `TabbedPage.OffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) Вложенное свойство зависимостей, и [ `SetOffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit(Xamarin.Forms.BindableObject,System.Int32)) метод, чтобы задать число страниц, в которых должны сохраняться в состоянии бездействия с обеих сторон текущей страницы.

Результатом является разбиение на страницы, проведите по экрану на страницах, отображаемого элементом [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) включен:

![](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
