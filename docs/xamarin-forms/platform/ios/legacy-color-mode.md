---
title: VisualElement прежних версий цветовой режим в iOS
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать iOS конкретных платформ, отключает режим прежних версий цвет Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 60FFBA67-6E06-439B-A5EB-8C808285E2CD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 28619f2bf1f864fc147ddaca2b8a59844e173b42
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209139"
---
# <a name="visualelement-legacy-color-mode-on-ios"></a>VisualElement прежних версий цветовой режим в iOS

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Некоторые представления Xamarin.Forms признаков устаревших цветовой режим. В этом режиме при [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) представления свойству `false`, представление будет переопределять цвета, установленные пользователем с помощью собственного цветов по умолчанию для отключенного состояния. Для обеспечения обратной совместимости, этот режим прежних версий цвет остается поведение по умолчанию для поддерживаемых представлений.

Этот iOS платформы отключает этот режим прежних версий цвет на [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), так что пользователь задал для представления цветов остаются даже при отключении представления. Он используется в XAML, задав [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) вложенное свойство, чтобы `false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для управления ли устаревших цветовой режим отключен. Кроме того [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})) метод может использоваться для возврата, не отключен ли устаревших цветовой режим.

Результатом является, что устаревшие цветовой режим можно отключить, таким образом, чтобы настроить в представлении пользователем цвета остаются даже при отключении представления:

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Устаревшие цветовой режим отключен")

> [!NOTE]
> При задании [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) для представления, устаревшие цветовой режим полностью игнорируется. Дополнительные сведения о визуальных состояниях см. в разделе [Xamarin.Forms Диспетчер визуальных состояний](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
