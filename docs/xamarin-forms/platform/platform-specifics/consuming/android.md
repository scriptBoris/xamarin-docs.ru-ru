---
title: Android особенностей платформы
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье показано, как использовать Android особенностей платформы, которые встроены в Xamarin.Forms.
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
ms.openlocfilehash: 5ed11e4afb4c061eb7b9dd8f10c67090b4134888
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996390"
---
# <a name="android-platform-specifics"></a>Android особенностей платформы

_Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье показано, как использовать Android особенностей платформы, которые встроены в Xamarin.Forms._

В Android Xamarin.Forms содержит следующие особенности платформы:

- Настройка режима работы экранной клавиатуры. Дополнительные сведения см. в разделе [режим входные данные мягкий клавиатуры](#soft_input_mode).
- Включение быстрой прокрутке в [ `ListView` ](xref:Xamarin.Forms.ListView) Дополнительные сведения см. в разделе [Включение быстрой прокрутке в ListView](#fastscroll).
- Включение считывания между страницами в [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Дополнительные сведения см. в разделе [Включение считывания между страницами в TabbedPage](#enable_swipe_paging).
- Управление Z-порядком визуальных элементов, чтобы определить порядок отображения. Дополнительные сведения см. в разделе [управление повышение прав визуальные элементы](#elevation).
- Отключение [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) и [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) странице события жизненного цикла на приостановить и возобновить соответственно, для приложений, использующих AppCompat. Дополнительные сведения см. в разделе [отключение Disappearing и появления событий жизненного цикла страницы](#disable_lifecycle_events).
- Управление ли [ `WebView` ](xref:Xamarin.Forms.WebView) можно отобразить смешанное содержимое. Дополнительные сведения см. в разделе [Включение смешанного содержимого в веб-представление](#webview-mixed-content).
- Настройка данный метод ввода параметров редактора для программируемой клавиатуры для [ `Entry` ](xref:Xamarin.Forms.Entry). Дополнительные сведения см. в разделе [параметры редактора метода ввода для записи параметра](#entry-imeoptions).
- Отключение режима устаревших цвет в поддерживаемой [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [отключение цветовой режим прежних версий](#legacy-color-mode).
- Используя Отбивка по умолчанию и значений тени Android кнопок. Дополнительные сведения см. в разделе [с помощью Android кнопки](#button-padding-shadow).
- Установка инструментов расположение и цвет для [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Дополнительные сведения см. в разделе [параметр TabbedPage инструментов размещения и цвет](#tabbedpage-toolbar).

<a name="soft_input_mode" />

## <a name="setting-the-soft-keyboard-input-mode"></a>Режим ввода программируемой клавиатуры

Этой платформы позволяет указать режим работы для области ввода программируемой клавиатуры и обрабатывается в XAML, задав [ `Application.WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) присоединенное свойство в значение [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) перечисление:

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

[![](android-images/pan-resize.png "Режим платформы операционной программируемой клавиатуры")](android-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Plaform-Specific")

<a name="fastscroll" />

## <a name="enabling-fast-scrolling-in-a-listview"></a>Включение быстрой прокрутке в ListView

Этой платформы используется для включения быстрой прокрутке данные в [ `ListView` ](xref:Xamarin.Forms.ListView). Он используется в XAML, задав `ListView.IsFastScrollEnabled` вложенное свойство, чтобы `boolean` значение:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

`ListView.On<Android>` Метод указывает, что этой платформы будет выполняться только в Android. `ListView.SetIsFastScrollEnabled` Метод в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) пространства имен, используемый для обеспечения быстрой прокрутке данные в [ `ListView` ](xref:Xamarin.Forms.ListView). Кроме того `SetIsFastScrollEnabled` метод можно использовать для переключения быстрой прокрутке путем вызова `IsFastScrollEnabled` метод для возврата, включен ли быстрый прокрутка:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

Результатом является то, что быстрой прокрутке данные в [ `ListView` ](xref:Xamarin.Forms.ListView) можно включить, который изменяет размер бегунка прокрутки:

[![](android-images/fastscroll.png "ListView FastScroll платформы")](android-images/fastscroll-large.png#lightbox "ListView FastScroll Plaform-Specific")

<a name="enable_swipe_paging" />

## <a name="enabling-swiping-between-pages-in-a-tabbedpage"></a>Включение считывания между страницами в TabbedPage

Этой платформы будет использоваться для включения, проведение пальцем по экрану с жест палец по горизонтали между страницами в [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Он используется в XAML, задав [ `TabbedPage.IsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) вложенное свойство, чтобы `boolean` значение:

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

![](android-images/tabbedpage-swipe.png)

<a name="elevation" />

## <a name="controlling-the-elevation-of-visual-elements"></a>Управление повышение прав визуальных элементов

Этой платформы, используемых для управления повышения прав, то есть Z-порядка визуальных элементов в приложениях, предназначенных для API 21 или более поздней версии. Повышение прав визуальный элемент, определяет порядок его отображения, с помощью визуальных элементов с более высоким значением Z, occluding визуальные элементы с более низкие значения Z. Он используется в XAML, задав `VisualElement.Elevation` вложенное свойство, чтобы `boolean` значение:

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

![](android-images/elevation.png)

<a name="disable_lifecycle_events" />

## <a name="disabling-the-disappearing-and-appearing-page-lifecycle-events"></a>Отключение события жизненного цикла приложения дело о Пропавшем и отображения объектов

Этой платформы используется для отключения [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) и [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) событий страницы в приложении, приостанавливать и возобновлять соответственно, для приложений, использующих совместимости приложений. Кроме того, он включает в себя возможность управления экранная клавиатура отображается на возобновление, если оно отображалось Пауза, при условии, что режим работы Экранная клавиатура, присваивается ли [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

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

[![](android-images/keyboard-on-resume.png "Жизненный цикл события платформы")](android-images/keyboard-on-resume-large.png#lightbox "жизненного цикла события платформы")

<a name="webview-mixed-content" />

## <a name="enabling-mixed-content-in-a-webview"></a>Включение смешанного содержимого в веб-представление

Эта определяет специфические для платформы ли [ `WebView` ](xref:Xamarin.Forms.WebView) можно Отображение разнородного содержимого в приложениях, предназначенных для API 21 или более поздней версии. Смешанное содержимое является содержимым, изначально загружается через HTTPS-соединение, но который загружает ресурсы (например, изображения, аудио, видео, таблицы стилей, сценарии) по протоколу HTTP. Он используется в XAML, задав [ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) присоединенное свойство в значение [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) перечисления:

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

[![WebView смешанный обработки содержимого платформы](android-images/webview-mixedcontent.png "WebView смешанный обработки содержимого платформы")](android-images/webview-mixedcontent-large.png#lightbox "WebView Смешанные платформы обработки содержимого")

<a name="entry-imeoptions" />

## <a name="setting-entry-input-method-editor-options"></a>Параметры редактора метода ввода для параметра записи

Этой платформы задает метод ввода параметров редактора (IME) для программируемой клавиатуры для [ `Entry` ](xref:Xamarin.Forms.Entry). Это включает в себя задание кнопки действие пользователя в нижнем углу программируемой клавиатуры и взаимодействия с `Entry`. Он используется в XAML, задав [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) присоединенное свойство в значение [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) перечисления:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

`Entry.On<Android>` Метод указывает, что этой платформы будет выполняться только в Android. [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) Метод в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) пространства имен, используется для задания параметра действия метода ввода для программируемой клавиатуры для [ `Entry` ](xref:Xamarin.Forms.Entry), с помощью [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) перечисления, предоставляя следующие значения:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) — Указывает, что ключ не определенное действие является обязательным, и что базового элемента управления будет создавать самостоятельно при его можно. Это будет `Next` или `Done`.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) — Указывает, что ключ не действия будут доступны.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) — Указывает, что ключ действия будет выполнять операции «go», когда пользователь в целевой объект текста, было введено.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) — Указывает, что ключ действия выполняет операцию «поиск», когда пользователь в результаты поиска в тексте будет их ввода.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) — Указывает, что ключ действия выполнит операцию «отправить», предоставляя текста своей цели.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) — Указывает, что ключ действия выполнит операцию «Далее», когда пользователь будет к следующему полю, которая будет принимать текст.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) — Указывает, что ключ действия выполнит операцию «Готово», закрытие экранной клавиатуры.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) — Указывает, что ключ действия выполнит операцию «раньше», когда пользователь будет к предыдущему полю, которая будет принимать текст.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) — маску для выбора параметров действия.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) — Указывает, что средство проверки орфографии будет ни Узнайте от пользователя, а также их решения в зависимости от того, что ранее введенное пользователем.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) — Указывает, что пользовательский Интерфейс не должен переходить в полноэкранном режиме.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) — Указывает, что пользовательский Интерфейс не отображается для извлеченного текста.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) — Указывает, что пользовательский Интерфейс не будет отображаться для выполнения пользовательских действий.

Результатом является то, что указанный [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) значение применяется к программируемой клавиатуры для [ `Entry` ](xref:Xamarin.Forms.Entry), присваивающего параметры редактора метода ввода:

[![Запись входных данных метод редактор платформы](android-images/entry-imeoptions.png "платформы редактора метода ввода запись")](android-images/entry-imeoptions-large.png#lightbox "платформы редактора метода ввода запись")

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>Отключение цветовой режим прежних версий

Некоторые представления Xamarin.Forms признаков устаревших цветовой режим. В этом режиме при [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) представления свойству `false`, представление будет переопределять цвета, установленные пользователем с помощью собственного цветов по умолчанию для отключенного состояния. Для обеспечения обратной совместимости, этот режим прежних версий цвет остается поведение по умолчанию для поддерживаемых представлений.

Этой платформы отключает этот режим прежних версий цвета, таким образом, чтобы настроить в представлении пользователем цвета остаются даже в том случае, если представление отключено. Он используется в XAML, задав [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) вложенное свойство, чтобы `false`:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Android>` Метод указывает, что этой платформы будет выполняться только в Android. [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) пространства имен, используется для управления ли устаревших цветовой режим отключен. Кроме того [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement})) метод может использоваться для возврата, не отключен ли устаревших цветовой режим.

Результатом является, что устаревшие цветовой режим можно отключить, таким образом, чтобы настроить в представлении пользователем цвета остаются даже при отключении представления:

![](android-images/legacy-color-mode-disabled.png "Устаревшие цветовой режим отключен")

> [!NOTE]
> При задании [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) для представления, устаревшие цветовой режим полностью игнорируется. Дополнительные сведения о визуальных состояниях см. в разделе [Xamarin.Forms Диспетчер визуальных состояний](~/xamarin-forms/user-interface/visual-state-manager.md).

<a name="button-padding-shadow" />

## <a name="using-android-buttons"></a>С помощью кнопок в Android

Этой платформы определяет, использовать ли кнопки Xamarin.Forms Отбивка по умолчанию и значений тени Android кнопок. Он используется в XAML, задав [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) и [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) присоединенных свойств для `boolean` значения:

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

`Button.On<Android>` Метод указывает, что этой платформы будет выполняться только в Android. [ `Button.SetUseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) И[ `Button.SetUseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) методы в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) пространства имен, используются для управления ли кнопки Xamarin.Forms использовать значение по умолчанию Заполнение и значений тени Android кнопок. Кроме того [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) и [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) методы можно использовать для возврата, использует ли кнопки по умолчанию, padding значение и значение по умолчанию тени, соответственно.

Результатом является то, что кнопки Xamarin.Forms можно использовать заполнение по умолчанию и значений тени Android кнопок:

![](android-images/button-padding-and-shadow.png "Устаревшие цветовой режим отключен")

Обратите внимание, что на снимке экрана выше [ `Button` ](xref:Xamarin.Forms.Button) имеет идентичные определений, за одним исключением правой `Button` использует Отбивка по умолчанию и значений тени Android кнопок.

<a name="tabbedpage-toolbar" />

## <a name="setting-tabbedpage-toolbar-placement-and-color"></a>Установка инструментов TabbedPage размещения и цвета

Эти особенности платформы позволяют задать положение и цвет элемента панели инструментов на [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Они используются в XAML, задав [ `TabbedPage.ToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.toolbarplacementproperty?view=xamarin-forms) присоединенное свойство в значение [ `ToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement?view=xamarin-forms) перечисления и [ `TabbedPage.BarItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.baritemcolorproperty?view=xamarin-forms) и [ `TabbedPage.BarSelectedItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.barselecteditemcolorproperty?view=xamarin-forms) присоединенных свойств для [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

Кроме того они могут использоваться из C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

`TabbedPage.On<Android>` Метод указывает, что эти особенности платформы будет выполняться только в Android. [ `TabbedPage.SetToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.settoolbarplacement?view=xamarin-forms) Метод в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) пространства имен, используемый для задания размещения панели инструментов на [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), с помощью [ `ToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement?view=xamarin-forms) перечисления, предоставляя следующие значения:

- [`Default`](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_ToolbarPlacement_Default) — Указывает, что панели инструментов размещается в расположении по умолчанию на странице. Это верхней части страницы на телефонах и нижней части страницы, на другие устройства выражения.
- [`Top`](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_ToolbarPlacement_Top) — Указывает, что панели инструментов размещается в верхней части страницы.
- [`Bottom`](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_ToolbarPlacement_Bottom) — Указывает, что панели инструментов размещается в нижней части страницы.

Кроме того [ `TabbedPage.SetBarItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.setbaritemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_SetBarItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__Xamarin_Forms_Color_) и [ `TabbedPage.SetBarSelectedItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.setbarselecteditemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_SetBarSelectedItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__Xamarin_Forms_Color_) методы используются для задания цвета элементов панели инструментов и элементов выбранной панели инструментов, соответственно.

> [!NOTE]
> [ `GetToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.gettoolbarplacement?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_GetToolbarPlacement_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__), [ `GetBarItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.getbaritemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_GetBarItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__), И [ `GetBarSelectedItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.getbarselecteditemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_GetBarSelectedItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__) методы могут использоваться для извлечения, расположение и цвет [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) панели инструментов.

Результатом является то, что можно задать расположение панели инструментов, цвет элементов панели инструментов и цвет элемента панели инструментов, выбранной на [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

![](android-images/tabbedpage-toolbar-placement.png)

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать Android особенностей платформы, которые встроены в Xamarin.Forms. Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов.

## <a name="related-links"></a>Связанные ссылки

- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
