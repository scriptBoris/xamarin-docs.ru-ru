---
title: Режим ввода программируемой клавиатуры в Android
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать Android конкретных платформ, задает режим для области ввода программируемой клавиатуры.
ms.prod: xamarin
ms.assetid: AFCDC92F-F61E-42F6-904B-50B5C4949970
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: c1955226f04203ad2e5805c47f35a32281942eab
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209199"
---
# <a name="soft-keyboard-input-mode-on-android"></a>Режим ввода программируемой клавиатуры в Android

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Это Android платформы позволяет указать режим работы для области ввода программируемой клавиатуры и потребляется в XAML, задав [ `Application.WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) присоединенное свойство в значение [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)перечисления:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

`Application.On<Android>` Метод указывает, что этой платформы будет выполняться только в Android. [ `Application.UseWindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)) Метод в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) пространства имен, используемый для задания режим работы программируемой клавиатуры область ввода, с помощью [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) Перечисление, предоставляя два значения: [ `Pan` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) и [ `Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize). `Pan` Значение использует [ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/) коррекции параметр, который не размера окна, когда элемент управления вводом в фокусе. Вместо этого содержимое окна сдвигаемых таким образом, чтобы текущий фокус не скрыт Экранная клавиатура. `Resize` Значение использует [ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/) коррекции параметр, который изменяет размер окна, когда элемента управления вводом в фокусе, чтобы освободить место для программируемой клавиатуры.

Результатом является, экранная клавиатура, входные данные области, которую можно задать режим работы, когда элемент управления вводом в фокусе:

[![](soft-keyboard-input-mode-images/pan-resize.png "Мягкая сочетания операционных режим платформы")](soft-keyboard-input-mode-images/pan-resize-large.png#lightbox "операционной режим платформы программируемой клавиатуры")

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
