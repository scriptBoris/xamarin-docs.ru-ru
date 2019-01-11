---
title: События жизненного цикла приложения на устройстве Android
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать Android платформы, который отключает Disappearing и Appearing событий страницы в приложении приостанавливать и возобновлять, соответственно.
ms.prod: xamarin
ms.assetid: F6E3759C-D347-407A-91A2-CF9B3B7D4CBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: ab9c404fc9051014fd3a243848290087f43a46d2
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209309"
---
# <a name="page-lifecycle-events-on-android"></a>События жизненного цикла приложения на устройстве Android

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Это Android платформы используется для отключения [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) и [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) событий страницы в приложении, приостанавливать и возобновлять соответственно, для приложений, использующих совместимости приложений. Кроме того, он включает в себя возможность управления экранная клавиатура отображается на возобновление, если оно отображалось Пауза, при условии, что режим работы Экранная клавиатура, присваивается ли [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

> [!NOTE]
> Обратите внимание на то, что эти события включены по умолчанию, чтобы сохранить существующее поведение для приложений, полагающихся на события. Отключение этих событий делает соответствует цикл pre-AppCompat AppCompat цикл.

Этой платформой могут использоваться в XAML, задав [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty), [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty), и [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) присоединенных свойств для `boolean` значения:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

`Application.Current.On<Android>` Метод указывает, что этой платформы будет выполняться только в Android. [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) пространство имен, используется для включения или отключения обработки [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) события страницы, когда приложение переходит в фоновом режиме. [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) Метод используется для включения или отключения обработки [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) события страницы, когда приложение возобновляет работу в фоновом режиме. [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) Используется метод управления ли экранная клавиатура отображается на возобновление, если оно отображалось Пауза, предоставленных режим работы Экранная клавиатура присваивается [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

В результате [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) и [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) событий страницы в Приостановка приложения не будет инициировать и возобновить соответственно, и был Экранная клавиатура, если отображается, когда приложение был приостановлен, он также отображается при возобновлении работы приложения:

[![](page-lifecycle-events-images/keyboard-on-resume.png "Жизненный цикл события платформы")](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "жизненного цикла события платформы")

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
