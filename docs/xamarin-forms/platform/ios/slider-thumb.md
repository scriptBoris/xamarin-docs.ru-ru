---
title: Коснитесь элемента Thumb "ползунок" в iOS
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать специфические для платформы iOS, позволяющий свойство Slider.Value для установки, нажав на панели "ползунок".
ms.prod: xamarin
ms.assetid: D0915D37-9A59-4728-BB6A-FE094A661275
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 43cc87f9d319295ce65d55488e1be032ae00a697
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208769"
---
# <a name="slider-thumb-tap-on-ios"></a>Коснитесь элемента Thumb "ползунок" в iOS

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Этот iOS платформы позволяет [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) свойство для задания можно просмотреть, выбрав для положения на [ `Slider` ](xref:Xamarin.Forms.Slider) панели, а не по необходимости перетащите `Slider` бегунка. Он используется в XAML, задав [ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) свойство, используемое для `true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

`Slider.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `Slider.SetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.SetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для управления ли касания на `Slider` установит панель [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) свойство. Кроме того [ `Slider.GetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.GetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider})) метод может использоваться для возврата ли касания на `Slider` установит панель `Slider.Value` свойство.

Результатом является, отвод на [ `Slider` ](xref:Xamarin.Forms.Slider) панели можно перемещать `Slider` бегунка и задать [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) свойство:

![](slider-thumb-images/slider-updateontap.png "Обновление ползунок на коснитесь включена")

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
