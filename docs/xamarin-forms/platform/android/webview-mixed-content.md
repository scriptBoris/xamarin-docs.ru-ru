---
title: Смешанное содержимое в Android WebView
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать этот целевой объект API 21 или более поздней версии Android конкретных платформ, отображаются в представлении, в приложениях смешанного содержимого.
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 7523862f3677eb775f59af0091ed59fec8c85e31
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209819"
---
# <a name="webview-mixed-content-on-android"></a>Смешанное содержимое в Android WebView

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Управляет этой Android платформы ли [ `WebView` ](xref:Xamarin.Forms.WebView) можно Отображение разнородного содержимого в приложениях, предназначенных для API 21 или более поздней версии. Смешанное содержимое является содержимым, изначально загружается через HTTPS-соединение, но который загружает ресурсы (например, изображения, аудио, видео, таблицы стилей, сценарии) по протоколу HTTP. Он используется в XAML, задав [ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) присоединенное свойство в значение [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) перечисления:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

`WebView.On<Android>` Метод указывает, что этой платформы будет выполняться только в Android. [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) Метод в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) пространства имен, используется для управления ли смешанное содержимое может отображаться, с помощью [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) Перечисление, предоставляя три возможных значения:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) — Указывает, что [ `WebView` ](xref:Xamarin.Forms.WebView) позволит источник HTTPS для загрузки содержимого из источника HTTP.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) — Указывает, что [ `WebView` ](xref:Xamarin.Forms.WebView) не позволит источник HTTPS для загрузки содержимого из источника HTTP.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) — Указывает, что [ `WebView` ](xref:Xamarin.Forms.WebView) попытается совместимы с подходом последние веб-браузере устройства. Часть содержимого HTTP может быть разрешен загружается источник HTTPS и другие типы содержимого будет заблокирован. Типы содержимого, заблокированы или разрешены могут быть изменены с каждым выпуском операционной системы.

Результатом является то, что указанный [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) значение применяется к [ `WebView` ](xref:Xamarin.Forms.WebView), который управляет отображением смешанное содержимое:

[![WebView смешанный обработки содержимого платформы](webview-mixed-content-images/webview-mixedcontent.png "WebView смешанный обработки содержимого платформы")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "WebView Смешанные платформы обработки содержимого")

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
