---
title: Особенности платформы iOS
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье показано, как использовать iOS особенностей платформы, которые встроены в Xamarin.Forms.
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: be378a60a9d9a7b206b64f07ee70edb432cec8e3
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935658"
---
# <a name="ios-platform-specifics"></a>Особенности платформы iOS

_Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье показано, как использовать iOS особенностей платформы, которые встроены в Xamarin.Forms._

В iOS Xamarin.Forms содержит следующие особенности платформы:

- Размытие поддержки для любого [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/). Дополнительные сведения см. в разделе [применения размытия](#blur).
- Управление, отображается ли заголовок страницы как крупным заголовком на панели навигации страницы. Дополнительные сведения см. в разделе [отображения крупных названий](#large_title).
- Обеспечение содержимое этой страницы находится на области экрана, которое безопасно для всех устройств iOS. Дополнительные сведения см. в разделе [включение безопасного направляющая макета области](#safe_area_layout).
- Панель навигации прозрачным. Дополнительные сведения см. в разделе [внесения прозрачные панели навигации](#translucent_navigation_bar).
- Управление цветом ли текст строки состояния на [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) настраивается в соответствии с яркость панели навигации. Дополнительные сведения см. в разделе [Настройка цветовой режим текст строки состояния](#status_bar_color_mode).
- Обеспечение, введена текст встраивается в [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) , корректируя размер шрифта. Дополнительные сведения см. в разделе [увеличением размера шрифта записи](#adjust_font_size).
- Управление, когда происходит выбор элементов в [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/). Дополнительные сведения см. в разделе [управление Выбор элемента](#picker_update_mode).
- Установка видимость полосы состояния [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). Дополнительные сведения см. в разделе [установки видимость полосы состояния на странице](#set_status_bar_visibility).
- Управление ли [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) жест касания обрабатывает или передает его на его содержимое. Дополнительные сведения см. в разделе [задержки содержимого штрихи в ScrollView](#delay_content_touches).
- Установка стиль разделителя [ `ListView` ](xref:Xamarin.Forms.ListView). Дополнительные сведения см. в разделе [параметр стиль разделителя на ListView](#listview-separatorstyle).
- Отключение режима устаревших цвет в поддерживаемой [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [отключение цветовой режим прежних версий](#legacy-color-mode).

<a name="blur" />

## <a name="applying-blur"></a>Применение размытия

Этой платформы используется Размытие содержимого располагаются под ним, а также используется в XAML, задав [ `VisualElement.BlurEffect` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty/) присоединенное свойство в значение [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) перечисления:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <AbsoluteLayout HorizontalOptions="Center">
      <Image Source="monkeyface.png" />
      <BoxView x:Name="boxView" ios:VisualElement.BlurEffect="ExtraLight" HeightRequest="300" WidthRequest="300" />
  </AbsoluteLayout>
  ...
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

`BoxView.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `VisualElement.UseBlurEffect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для применения эффекта размытия, с помощью [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) перечисления, предоставляя четыре значения: [ `None` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None), [ `ExtraLight` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight), [ `Light` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light), и [ `Dark` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark).

Результатом является то, что указанный [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) применяется к [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) какие Размытие, экземпляр [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) располагаются под ним:

![](ios-images/blur-effect.png "Чтобы минимизировать эффект от платформы")

<a name="large_title" />

## <a name="displaying-large-titles"></a>Отображение крупных названий

Этой платформы, используемый для отображения заголовка страницы как крупным заголовком на панели навигации, для устройств, использующих iOS 11 или выше. Большой Заголовок выравнивается по левому краю и использует крупный шрифт и переходит в стандартный заголовок как пользователь начинает прокрутка содержимого, чтобы использовалась площади экрана эффективно. Однако в альбомной ориентации заголовок будет возвращать относительно центральной части панели навигации для оптимизации макета содержимого. Он используется в XAML, задав `NavigationPage.PrefersLargeTitles` вложенное свойство, чтобы `boolean` значение:

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

`NavigationPage.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `NavigationPage.SetPrefersLargeTitle` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) пространства имен, управляющий включением крупных названий.

Условии, что включены крупных названий [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), все страницы в стеке навигации будут отображены в крупных названий. Это поведение можно переопределить на страницах, задав `Page.LargeTitleDisplay` присоединенное свойство в значение `LargeTitleDisplayMode` перечисления:

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

`Page.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `Page.SetLargeTitleDisplay` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) пространства имен, управляет поведением большого заголовка на [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), с помощью `LargeTitleDisplayMode` перечисления, предоставляя три возможных значения:

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

Результатом является то, что указанный `LargeTitleDisplayMode` применяется к [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), которое управляет поведением большого заголовка:

![](ios-images/large-title.png "Чтобы минимизировать эффект от платформы")

<a name="safe_area_layout" />

## <a name="enabling-the-safe-area-layout-guide"></a>Включение направляющая макета безопасной области

Этой платформы позволяет убедиться, что содержимое страницы расположен на область экрана, которое безопасно для всех устройств, использующих iOS 11 и выше. В частности, полезно убедитесь, что содержимое не попадают в устройство со скругленными углами, индикатор домашней или корпусе датчика на iPhone X. Он используется в XAML, задав `Page.UseSafeArea` вложенное свойство, чтобы `boolean` значение:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

`Page.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `Page.SetUseSafeArea` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) пространства имен, определяет, включена ли направляющая макета безопасной области.

Результатом является то, что содержимое страницы может располагаться на той части экрана, которое безопасно для всех iPhone:

[![](ios-images/safe-area-layout.png "Направляющая макета безопасной области")](ios-images/safe-area-layout-large.png#lightbox "направляющая макета безопасной области")

> [!NOTE]
> Безопасные области, определяемой Apple, используемый для задания в Xamarin.Forms [ `Page.Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/) свойство и переопределяет все предыдущие значения этого свойства, которые были установлены.

Безопасной области можно настраивать путем извлечения его [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) со значением `Page.SafeAreaInsets` метода из [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) пространства имен. Его можно изменить как требуется и переназначается `Padding` свойства в конструкторе страниц или [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) переопределить:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

<a name="translucent_navigation_bar" />

## <a name="making-the-navigation-bar-translucent"></a>Что делает прозрачным на панели навигации

Используется для изменения прозрачности панели навигации этой платформы и обрабатывается в XAML, задав [ `NavigationPage.IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty/) вложенное свойство, чтобы `boolean` значение:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

`NavigationPage.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `NavigationPage.EnableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) пространства имен, используется для выполнения на панели навигации прозрачным. Кроме того [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage/) в класс `Xamarin.Forms.PlatformConfiguration.iOSSpecific` пространство имен также содержит [ `DisableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) метод, который восстанавливает состояние по умолчанию, на панели навигации и [ `SetIsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/System.Boolean/) метод, который может использоваться для переключения прозрачность панель навигации, вызвав [ `IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) метод:

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

Результатом является то, что может быть изменена прозрачность на панели навигации:

![](ios-images/translucent-navigation-bar.png "Прозрачные панели навигации платформы")

<a name="status_bar_color_mode" />

## <a name="adjusting-the-status-bar-text-color-mode"></a>Настройка строки текстовый режим цвета состояния

Эта определяет специфические для платформы ли текст строки состояния цвета на [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) настраивается в соответствии с яркость панели навигации. Он используется в XAML, задав [ `NavigationPage.StatusBarTextColorMode` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty/) присоединенное свойство в значение [ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) перечисления:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

`NavigationPage.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `NavigationPage.SetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) пространства имен, элементы управления цветом ли текст строки состояния на [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) настраивается в соответствии с яркость панели навигации с [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) перечисления, предоставляя два возможных значения:

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) — Указывает, что состояние цвет текста столбца не должно изменяться.
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) — Указывает, что строки цвет текста состояния должен искать яркость панели навигации.

Кроме того [ `GetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/) метод может использоваться для получения текущего значения [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) перечисления, который применяется к [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage).

Результатом является, строке цвета текста и состояния [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) можно изменить в соответствии с яркость панели навигации. В этом примере состояние изменения цвета текста строки как пользователь переключается между [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) и [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) страницы [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

![](ios-images/status-bar-text-color-mode.png "Строка состояния текста цветов режим платформы")

<a name="adjust_font_size" />

## <a name="adjusting-the-font-size-of-an-entry"></a>Изменение размера шрифта записи

Этой платформы позволяет масштабировать размер шрифта [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) чтобы убедиться, что введено текст помещается в элемент управления. Он используется в XAML, задав [ `Entry.AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty/) вложенное свойство, чтобы `boolean` значение:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

`Entry.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `Entry.EnableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) пространства имен, используется для масштабирования размер шрифта текста введено, чтобы убедиться, что она помещается в [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/). Кроме того [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry/) в класс `Xamarin.Forms.PlatformConfiguration.iOSSpecific` пространство имен также содержит [ `DisableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) метод, который отключает этой платформой, и [ `SetAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/System.Boolean/) метод, который может использоваться для переключения размер шрифта, масштабирование, вызвав [ `AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) метод:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

Результатом является, размер шрифта [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) масштабируется, чтобы убедиться, что введено текст помещается в элемент управления:

![](ios-images/entry-font-size.png "Настроить запись шрифта размер платформы")

<a name="picker_update_mode" />

## <a name="controlling-picker-item-selection"></a>Выбор элементов управления средства выбора

Управляет этой платформы, при возникновении Выбор элементов в [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), позволяя пользователю указать, что выбор элементов срабатывает при просмотре элементов в элементе управления или только в том случае, когда **сделать** нажата кнопка. Он используется в XAML, задав `Picker.UpdateMode` присоединенное свойство в значение `UpdateMode` перечисления:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`Picker.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `Picker.SetUpdateMode` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) пространства имен, используется для управления, когда происходит выбор элементов, с помощью `UpdateMode` перечисления, предоставляя два возможных значения:

- `Immediately` — Выбор элемента происходит, когда пользователь выбирает элементы в [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/). Это поведение по умолчанию в Xamarin.Forms.
- `WhenFinished` — Выбор элементов только происходит, когда пользователь нажал **сделать** кнопку [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/).

Кроме того `SetUpdateMode` метод может использоваться для включения значений перечисления, вызвав `UpdateMode` метод, который возвращает текущий `UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

Результатом является то, что указанный `UpdateMode` применяется к [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), какие элементы управления, когда происходит выбор элементов:

[![](ios-images/picker-updatemode.png "Средство выбора UpdateMode специфические для платформы")](ios-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Plaform-Specific")

<a name="set_status_bar_visibility" />

## <a name="setting-the-status-bar-visibility-on-a-page"></a>Параметр в строке состояния видимости на странице

Этой платформы позволяет настроить видимость для строки состояния на [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), а также возможность управлять как строка состояния или исключается из `Page`. Он используется в XAML, задав `Page.PrefersStatusBarHidden` присоединенное свойство в значение `StatusBarHiddenMode` перечисления и при необходимости `Page.PreferredStatusBarUpdateAnimation` присоединенное свойство в значение `UIStatusBarAnimation` перечисления:

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

`Page.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `Page.SetPrefersStatusBarHidden` Метод в `Xamarin.Forms.PlatformConfiguration.iOSSpecific` пространства имен, используемый для задания видимость строки состояния на [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) , указав одно из `StatusBarHiddenMode` значений перечисления: `Default`, `True` , или `False`. `StatusBarHiddenMode.True` И `StatusBarHiddenMode.False` видимость полосы состояния вне зависимости от ориентации устройства, вы задаете и `StatusBarHiddenMode.Default` значение скрывает строку состояния в среде по вертикали compact.

Результатом является, видимость строки состояния [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) можно задать:

![](ios-images/hide-status-bar.png "Строка состояния видимости специфические для платформы")

> [!NOTE]
> На [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), указанный `StatusBarHiddenMode` значение перечисления также будет обновить строку состояния на все дочерние страницы. Во всех остальных [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-производные типы, указанного `StatusBarHiddenMode` значение перечисления будут обновлены только строке состояния на текущей странице.

`Page.SetPreferredStatusBarUpdateAnimation` Метод используется для задания как в строке состояния или исключается из [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) , указав одно из `UIStatusBarAnimation` значений перечисления: `None`, `Fade`, или `Slide`. Если `Fade` или `Slide` указано значение перечисления, 0,25 второй анимации выполняются как строка состояния или исключается из `Page`.

<a name="delay_content_touches" />

## <a name="delaying-content-touches-in-a-scrollview"></a>Задержка содержимого штрихи в ScrollView

Таймеру неявное активируется в том случае, когда начинается жест касания в [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) в iOS и `ScrollView` решает, в зависимости от действий пользователя в течение интервала таймера, он должен обрабатывать жест или передайте его на его содержимое. По умолчанию iOS `ScrollView` содержимого штрихи задержки, но это может вызвать проблемы в некоторых случаях при работе с `ScrollView` содержимое, не выиграть жест, когда это требуется. Таким образом, эта определяет специфические для платформы ли `ScrollView` жест касания обрабатывает или передает его на его содержимое. Он используется в XAML, задав `ScrollView.ShouldDelayContentTouches` вложенное свойство, чтобы `boolean` значение:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

`ScrollView.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `ScrollView.SetShouldDelayContentTouches` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) пространства имен, используемый для управления ли [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) жест касания обрабатывает или передает его на его содержимое. Кроме того `SetShouldDelayContentTouches` метод можно использовать для переключения задержки содержимого штрихи, вызвав `ShouldDelayContentTouches` метод для возврата, отложена ли штрихи содержимого:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

В результате [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) можно отключить, задерживая получение содержимого штрихи, таким образом, в этом сценарии [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) получает жест, а не [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) странице [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

[![](ios-images/scrollview-delay-content-touches.png "Задержка ScrollView содержимое затрагивает специфические для платформы")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Plaform-Specific")

<a name="listview-separatorstyle" />

## <a name="setting-the-separator-style-on-a-listview"></a>Установка стиль разделителя для ListView

Управляет этой платформы ли разделитель между ячейки в [ `ListView` ](xref:Xamarin.Forms.ListView) использует всю ширину `ListView`. Он используется в XAML, задав [ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) присоединенное свойство в значение [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) перечисления:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

`ListView.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для управления ли разделитель между ячейки в [ `ListView` ](xref:Xamarin.Forms.ListView) использует полную Ширина `ListView`, с помощью [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) перечисления, предоставляя два возможных значения:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) — Задает поведение разделителя по умолчанию для операций ввода-вывода. Это поведение по умолчанию в Xamarin.Forms.
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) — Указывает, что разделители получаются от одного края `ListView` в другой.

Результатом является то, что указанный [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) значение применяется к [ `ListView` ](xref:Xamarin.Forms.ListView), который управляет шириной разделителя между ячейками:

![](ios-images/listview-separatorstyle.png "ListView SeparatorStyle платформы")

> [!NOTE]
> После установки стиль разделителя `FullWidth`, не может изменить его статус на `Default` во время выполнения.

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>Отключение цветовой режим прежних версий

Некоторые представления Xamarin.Forms признаков устаревших цветовой режим. В этом режиме при [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) представления свойству `false`, представление будет переопределять цвета, установленные пользователем с помощью собственного цветов по умолчанию для отключенного состояния. Для обеспечения обратной совместимости, этот режим прежних версий цвет остается поведение по умолчанию для поддерживаемых представлений.

Этой платформы отключает этот режим прежних версий цвета, таким образом, чтобы настроить в представлении пользователем цвета остаются даже в том случае, если представление отключено. Он используется в XAML, задав [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) вложенное свойство, чтобы `false`:

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

![](ios-images/legacy-color-mode-disabled.png "Устаревшие цветовой режим отключен")

> [!NOTE]
> При задании [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) для представления, устаревшие цветовой режим полностью игнорируется. Дополнительные сведения о визуальных состояниях см. в разделе [Xamarin.Forms Диспетчер визуальных состояний](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать iOS особенностей платформы, которые встроены в Xamarin.Forms. Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов.


## <a name="related-links"></a>Связанные ссылки

- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)
