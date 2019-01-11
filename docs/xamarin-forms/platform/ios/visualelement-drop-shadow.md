---
title: Shadows Drop VisualElement в iOS
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать iOS конкретных платформ, позволяющий тени на VisualElement.
ms.prod: xamarin
ms.assetid: 2147FD66-058E-4BE5-840A-369842B26EC4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 1019ecb6f5311d34a38cb0d330b25fc1e25b5c41
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209009"
---
# <a name="visualelement-drop-shadows-on-ios"></a>Shadows Drop VisualElement в iOS

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Этот iOS платформы используется для включения тени на [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Он используется в XAML, задав [ `VisualElement.IsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) вложенное свойство, чтобы `true`, а также ряд дополнительных необязательно присоединенного свойства, которые управляют тени:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <BoxView ...
                 ios:VisualElement.IsShadowEnabled="true"
                 ios:VisualElement.ShadowColor="Purple"
                 ios:VisualElement.ShadowOpacity="0.7"
                 ios:VisualElement.ShadowRadius="12">
            <ios:VisualElement.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </ios:VisualElement.ShadowOffset>
         </BoxView>
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var boxView = new BoxView { Color = Color.Aqua, WidthRequest = 100, HeightRequest = 100 };
boxView.On<iOS>()
       .SetIsShadowEnabled(true)
       .SetShadowColor(Color.Purple)
       .SetShadowOffset(new Size(10,10))
       .SetShadowOpacity(0.7)
       .SetShadowRadius(12);
```

`VisualElement.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `VisualElement.SetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для управления отвечает за включение тени на `VisualElement`. Кроме того можно вызвать следующие методы для управления тени:

- [`SetShadowColor`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Color)) — Задает цвет тени. Цвет по умолчанию [ `Color.Default` ](xref:Xamarin.Forms.Color.Default*).
- [`SetShadowOffset`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Size)) — Задает смещение тени. Смещение меняет направление тени приводится и указываются в виде [ `Size` ](xref:Xamarin.Forms.Size) значение. `Size` Структуры значения выражаются в аппаратно независимых единицах, первое значение, что расстояние до (отрицательное значение) или влево (положительное значение), а второе значение, что расстояние выше (отрицательное значение) или ниже (положительное значение) . Значение этого свойства по умолчанию — (0.0, 0.0), тень, что приводит к приведение вокруг каждая сторона `VisualElement`.
- [`SetShadowOpacity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) — Задает уровень непрозрачности тени, со значением, в диапазоне от 0.0 (прозрачный) до версии 1.0 (Непрозрачный). Значение непрозрачности по умолчанию — 0,5.
- [`SetShadowRadius`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) — Задает радиуса размытия, используемую для отрисовки тени. Значение радиуса по умолчанию — 10.0.

> [!NOTE]
> Состояние тени можно запрашивать путем вызова [ `GetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowOffset` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowOpacity` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), и [ `GetShadowRadius` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})) методы.

Результатом является то, что тень можно включить на [ `VisualElement` ](xref:Xamarin.Forms.VisualElement):

![](drop-shadow-images/drop-shadow.png "Тень включена")

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
