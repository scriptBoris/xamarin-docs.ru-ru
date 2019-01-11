---
title: Повышение прав VisualElement в Android
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать Android конкретных платформ, управляющий несанкционированному VisualElements приложений, предназначенных для API 21 или более поздней версии.
ms.prod: xamarin
ms.assetid: 5BFD6175-2BBD-41CD-B8F9-521B4750B708
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: b3dd300d28e0cf27cc1b5ebea59a68d57145fd61
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208929"
---
# <a name="visualelement-elevation-on-android"></a>Повышение прав VisualElement в Android

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Это Android специфические для платформы, используемых для управления повышения прав, то есть Z-порядка визуальных элементов в приложениях, предназначенных для API 21 или более поздней версии. Повышение прав визуальный элемент, определяет порядок его отображения, с помощью визуальных элементов с более высоким значением Z, occluding визуальные элементы с более низкие значения Z. Он используется в XAML, задав `VisualElement.Elevation` вложенное свойство, чтобы `boolean` значение:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:VisualElement.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

`Button.On<Android>` Метод указывает, что этой платформы будет выполняться только в Android. `VisualElement.SetElevation` Метод в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) пространства имен, используемый для задания высоту данного визуального элемента значения NULL `float`. Кроме того `VisualElement.GetElevation` метод может использоваться для получения значения повышение прав визуальный элемент.

Результатом является то, что повышение прав визуальных элементов можно управлять таким образом, чтобы визуальные элементы с более высоким значением Z скрывать визуальные элементы с более низкие значения Z. Таким образом, в этом примере второй [ `Button` ](xref:Xamarin.Forms.Button) отображается над [ `BoxView` ](xref:Xamarin.Forms.BoxView) так как он имеет более высокое значение повышения прав:

![](visualelement-elevation-images/elevation.png)

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
