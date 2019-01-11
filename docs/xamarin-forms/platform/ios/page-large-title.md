---
title: Большой заголовки страниц в iOS
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать iOS конкретных платформ, отображаемую в виде больших заголовок на панели навигации NavigationPage заголовок страницы.
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: ad1c7f94467c6f32b9108ab7f6abe85d31679d3a
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208989"
---
# <a name="large-page-titles-on-ios"></a>Большой заголовки страниц в iOS

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Этот iOS платформы используется для отображения заголовка страницы как крупным заголовком на панели навигации [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), для устройств, использующих iOS 11 или выше. Большой Заголовок выравнивается по левому краю и использует крупный шрифт и переходит в стандартный заголовок как пользователь начинает прокрутка содержимого, чтобы использовалась площади экрана эффективно. Однако в альбомной ориентации заголовок будет возвращать относительно центральной части панели навигации для оптимизации макета содержимого. Он используется в XAML, задав `NavigationPage.PrefersLargeTitles` вложенное свойство, чтобы `boolean` значение:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

Также его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

`NavigationPage.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `NavigationPage.SetPrefersLargeTitle` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, управляющий включением крупных названий.

Условии, что включены крупных названий [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), все страницы в стеке навигации будут отображены в крупных названий. Это поведение можно переопределить на страницах, задав `Page.LargeTitleDisplay` присоединенное свойство в значение `LargeTitleDisplayMode` перечисления:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Кроме того поведение страницы может быть переопределено с помощью текучего API в C#:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

`Page.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `Page.SetLargeTitleDisplay` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, управляет поведением большого заголовка на [ `Page` ](xref:Xamarin.Forms.Page), с помощью `LargeTitleDisplayMode` перечисления, предоставляя три возможных значения:

- `Always` — Принудительное панели навигации и шрифт размер для использования большого формата.
- `Automatic` — использовать один и тот же стиль (больших или маленьких) с предыдущим элементом в стеке навигации.
- `Never` — принудительно использовать панель навигации регулярных "," мелкая форматирования.

Кроме того `SetLargeTitleDisplay` метод может использоваться для включения значений перечисления, вызвав `LargeTitleDisplay` метод, который возвращает текущий `LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

Результатом является то, что указанный `LargeTitleDisplayMode` применяется к [ `Page` ](xref:Xamarin.Forms.Page), которое управляет поведением большого заголовка:

![](page-large-title-images/large-title.png "Чтобы минимизировать эффект от платформы")

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
