---
title: Видимость полосы состояние страницы в iOS
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать iOS конкретных платформ, задает видимость строки состояния на странице.
ms.prod: xamarin
ms.assetid: D8BB7C24-A27F-4758-8557-6A81F909ABD9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 98eba6dea1fb528aa15a1fb242b0fb0eb7dada56
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208709"
---
# <a name="page-status-bar-visibility-on-ios"></a>Видимость полосы состояние страницы в iOS

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Этот iOS платформы используется для задания видимость строки состояния на [ `Page` ](xref:Xamarin.Forms.Page), а также возможность управлять как строка состояния или исключается из `Page`. Он используется в XAML, задав `Page.PrefersStatusBarHidden` присоединенное свойство в значение `StatusBarHiddenMode` перечисления и при необходимости `Page.PreferredStatusBarUpdateAnimation` присоединенное свойство в значение `UIStatusBarAnimation` перечисления:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

`Page.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `Page.SetPrefersStatusBarHidden` Метод в `Xamarin.Forms.PlatformConfiguration.iOSSpecific` пространства имен, используемый для задания видимость строки состояния на [ `Page` ](xref:Xamarin.Forms.Page) , указав одно из `StatusBarHiddenMode` значений перечисления: `Default`, `True` , или `False`. `StatusBarHiddenMode.True` И `StatusBarHiddenMode.False` видимость полосы состояния вне зависимости от ориентации устройства, вы задаете и `StatusBarHiddenMode.Default` значение скрывает строку состояния в среде по вертикали compact.

Результатом является, видимость строки состояния [ `Page` ](xref:Xamarin.Forms.Page) можно задать:

![](page-status-bar-visibility-images/hide-status-bar.png "Строка состояния видимости специфические для платформы")

> [!NOTE]
> На [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), указанный `StatusBarHiddenMode` значение перечисления также будет обновить строку состояния на все дочерние страницы. Во всех остальных [ `Page` ](xref:Xamarin.Forms.Page)-производные типы, указанного `StatusBarHiddenMode` значение перечисления будут обновлены только строке состояния на текущей странице.

`Page.SetPreferredStatusBarUpdateAnimation` Метод используется для задания как в строке состояния или исключается из [ `Page` ](xref:Xamarin.Forms.Page) , указав одно из `UIStatusBarAnimation` значений перечисления: `None`, `Fade`, или `Slide`. Если `Fade` или `Slide` указано значение перечисления, 0,25 второй анимации выполняются как строка состояния или исключается из `Page`.

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
