---
title: Особенности платформы iOS
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье показано, как использовать iOS особенностей платформы, которые встроены в Xamarin.Forms.
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: afecf5c173e919bd20015aadd8a874f492dc4e34
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527083"
---
# <a name="ios-platform-specifics"></a>Особенности платформы iOS

_Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье показано, как использовать iOS особенностей платформы, которые встроены в Xamarin.Forms._

## <a name="visualelements"></a>VisualElements

В iOS следующие функциональные возможности платформы предоставляется для представления, страницы и макеты Xamarin.Forms:

- Размытие поддержки для любого [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [применения размытия](#blur).
- Отключение режима устаревших цвет в поддерживаемой [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [отключение цветовой режим прежних версий](#legacy-color-mode).
- Включение тень [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [Включение падающую тень](#drop-shadow).

<a name="blur" />

### <a name="applying-blur"></a>Применение размытия

Этой платформы используется Размытие содержимого располагаются под ним, а также используется в XAML, задав [ `VisualElement.BlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty) присоединенное свойство в значение [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) перечисления:

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

`BoxView.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `VisualElement.UseBlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для применения эффекта размытия, с помощью [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) перечисления, предоставляя четыре значения: [ `None` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None), [ `ExtraLight` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight), [ `Light` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light), и [ `Dark` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark).

Результатом является то, что указанный [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) применяется к [ `BoxView` ](xref:Xamarin.Forms.BoxView) какие Размытие, экземпляр [ `Image` ](xref:Xamarin.Forms.Image) располагаются под ним:

![](ios-images/blur-effect.png "Чтобы минимизировать эффект от платформы")

> [!NOTE]
> При добавлении эффект размытия для [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), события касания будет по-прежнему получать `VisualElement`.

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>Отключение цветовой режим прежних версий

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

<a name="drop-shadow" />

### <a name="enabling-a-drop-shadow"></a>Включение тени

Этой платформы будет использоваться для включения тени на [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Он используется в XAML, задав [ `VisualElement.IsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) вложенное свойство, чтобы `true`, а также ряд дополнительных необязательно присоединенного свойства, которые управляют тени:

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

![](ios-images/drop-shadow.png "Тень включена")

## <a name="views"></a>Представления

В iOS предоставляется следующие функциональные возможности платформы для представления Xamarin.Forms:

- Обеспечение, введена текст встраивается в [ `Entry` ](xref:Xamarin.Forms.Entry) , корректируя размер шрифта. Дополнительные сведения см. в разделе [увеличением размера шрифта записи](#adjust_font_size).
- Задание цвета курсора [ `Entry` ](xref:Xamarin.Forms.Entry). Дополнительные сведения см. в разделе [задания цвета курсора запись](#entry-cursorcolor).
- Установка стиль разделителя [ `ListView` ](xref:Xamarin.Forms.ListView). Дополнительные сведения см. в разделе [параметр стиль разделителя на ListView](#listview-separatorstyle).
- Управление, когда происходит выбор элементов в [ `Picker` ](xref:Xamarin.Forms.Picker). Дополнительные сведения см. в разделе [управление Выбор элемента](#picker_update_mode).
- Включение [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) свойство для задания можно просмотреть, выбрав для положения на [ `Slider` ](xref:Xamarin.Forms.Slider) панели, а не по необходимости перетащите `Slider` бегунка. Дополнительные сведения см. в разделе [Включение ползунка для перемещения на коснитесь](#slider-updateontap).

<a name="adjust_font_size" />

### <a name="adjusting-the-font-size-of-an-entry"></a>Изменение размера шрифта записи

Этой платформы позволяет масштабировать размер шрифта [ `Entry` ](xref:Xamarin.Forms.Entry) чтобы убедиться, что введено текст помещается в элемент управления. Он используется в XAML, задав [ `Entry.AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) вложенное свойство, чтобы `boolean` значение:

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

`Entry.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `Entry.EnableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для масштабирования размер шрифта текста введено, чтобы убедиться, что она помещается в [ `Entry` ](xref:Xamarin.Forms.Entry). Кроме того [ `Entry` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry) в класс `Xamarin.Forms.PlatformConfiguration.iOSSpecific` пространство имен также содержит [ `DisableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) метод, который отключает этой платформой, и [ `SetAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},System.Boolean)) метод, который может использоваться для переключения размер шрифта, масштабирование, вызвав [ `AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) метод:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

Результатом является, размер шрифта [ `Entry` ](xref:Xamarin.Forms.Entry) масштабируется, чтобы убедиться, что введено текст помещается в элемент управления:

![](ios-images/entry-font-size.png "Настроить запись шрифта размер платформы")

<a name="entry-cursorcolor" />

### <a name="setting-the-entry-cursor-color"></a>Задание цвета запись курсора

Этой платформы задает цвет курсора в [ `Entry` ](xref:Xamarin.Forms.Entry) для выбранного цвета. Он используется в XAML, задав [ `Entry.CursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.CursorColorProperty) свойство, используемое для [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry ... ios:Entry.CursorColor="LimeGreen" />
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var entry = new Xamarin.Forms.Entry();
entry.On<iOS>().SetCursorColor(Color.LimeGreen);
```

`Entry.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `Entry.SetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},Xamarin.Forms.Color)) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, задает цвет курсора с заданным [ `Color` ](xref:Xamarin.Forms.Color). Кроме того [ `Entry.GetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.GetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) метод может использоваться для получения текущий цвет курсора.

Результатом является, цвет курсора в [ `Entry` ](xref:Xamarin.Forms.Entry) можно задать с определенным [ `Color` ](xref:Xamarin.Forms.Color):

![](ios-images/entry-cursorcolor.png "Цвет курсора запись")

<a name="listview-separatorstyle" />

### <a name="setting-the-separator-style-on-a-listview"></a>Установка стиль разделителя для ListView

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

<a name="picker_update_mode" />

### <a name="controlling-picker-item-selection"></a>Выбор элементов управления средства выбора

Управляет этой платформы, при возникновении Выбор элементов в [ `Picker` ](xref:Xamarin.Forms.Picker), позволяя пользователю указать, что выбор элементов срабатывает при просмотре элементов в элементе управления или только в том случае, когда **сделать** нажата кнопка. Он используется в XAML, задав `Picker.UpdateMode` присоединенное свойство в значение `UpdateMode` перечисления:

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

`Picker.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `Picker.SetUpdateMode` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для управления, когда происходит выбор элементов, с помощью `UpdateMode` перечисления, предоставляя два возможных значения:

- `Immediately` — Выбор элемента происходит, когда пользователь выбирает элементы в [ `Picker` ](xref:Xamarin.Forms.Picker). Это поведение по умолчанию в Xamarin.Forms.
- `WhenFinished` — Выбор элементов только происходит, когда пользователь нажал **сделать** кнопку [ `Picker` ](xref:Xamarin.Forms.Picker).

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

Результатом является то, что указанный `UpdateMode` применяется к [ `Picker` ](xref:Xamarin.Forms.Picker), какие элементы управления, когда происходит выбор элементов:

[![](ios-images/picker-updatemode.png "Средство выбора UpdateMode платформы")](ios-images/picker-updatemode-large.png#lightbox "выбора UpdateMode специфические для платформы")

<a name="slider-updateontap" />

### <a name="enabling-a-slider-thumb-to-move-on-tap"></a>Включение ползунка для перемещения на Tap

Этой платформы позволяет [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) свойство для задания можно просмотреть, выбрав для положения на [ `Slider` ](xref:Xamarin.Forms.Slider) панели, а не по необходимости перетащите `Slider` бегунка. Он используется в XAML, задав [ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) свойство, используемое для `true`:

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

![](ios-images/slider-updateontap.png "Обновление ползунок на коснитесь включена")

## <a name="pages"></a>Pages

В iOS следующие функциональные возможности платформы предоставляется для страницы Xamarin.Forms:

- Скрытие разделителя панели навигации на [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Дополнительные сведения см. в разделе [скрытие разделителя панели навигации на NavigationPage](#navigationpage-hideseparatorbar).
- Управление, является ли прозрачные панели навигации. Дополнительные сведения см. в разделе [внесения прозрачные панели навигации](#translucent_navigation_bar).
- Управление цветом ли текст строки состояния на [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) настраивается в соответствии с яркость панели навигации. Дополнительные сведения см. в разделе [Настройка цветовой режим текст строки состояния](#status_bar_color_mode).
- Управление, отображается ли заголовок страницы как крупным заголовком на панели навигации страницы. Дополнительные сведения см. в разделе [отображения крупных названий](#large_title).
- Установка видимость полосы состояния [ `Page` ](xref:Xamarin.Forms.Page). Дополнительные сведения см. в разделе [установки видимость полосы состояния на странице](#set_status_bar_visibility).
- Обеспечение содержимое этой страницы находится на области экрана, которое безопасно для всех устройств iOS. Дополнительные сведения см. в разделе [включение безопасного направляющая макета области](#safe_area_layout).
- Настройка стиля представления модальные страницы на iPad. Дополнительные сведения см. в разделе [параметр стиля презентации модальные страницы на iPad](#modal-page-presentation-style).

<a name="navigationpage-hideseparatorbar" />

### <a name="hiding-the-navigation-bar-separator-on-a-navigationpage"></a>Скрытие панели навигации разделитель NavigationPage

Скрывает этой платформы разделительной линии и тень, расположенной в нижней части панели навигации [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Он используется в XAML, задав [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) свойство, используемое для `false`:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

`NavigationPage.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `NavigationPage.SetHideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetHideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для управления ли разделителя панели навигации скрыта. Кроме того [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) метод может использоваться для возврата, скрыт ли разделителя панели навигации.

Результатом является, разделителя панели навигации на [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) можно скрыть:

![](ios-images/navigationpage-hideseparatorbar.png "Панель навигации NavigationPage скрыто")

<a name="translucent_navigation_bar" />

### <a name="making-the-navigation-bar-translucent"></a>Что делает прозрачным на панели навигации

Используется для изменения прозрачности панели навигации этой платформы и обрабатывается в XAML, задав [ `NavigationPage.IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty) вложенное свойство, чтобы `boolean` значение:

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

`NavigationPage.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `NavigationPage.EnableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для выполнения на панели навигации прозрачным. Кроме того [ `NavigationPage` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage) в класс `Xamarin.Forms.PlatformConfiguration.iOSSpecific` пространство имен также содержит [ `DisableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) метод, который восстанавливает состояние по умолчанию, на панели навигации и [ `SetIsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean)) метод, который может использоваться для переключения прозрачность панель навигации, вызвав [ `IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) метод:

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

Результатом является то, что может быть изменена прозрачность на панели навигации:

![](ios-images/translucent-navigation-bar.png "Прозрачные панели навигации платформы")

<a name="status_bar_color_mode" />

### <a name="adjusting-the-status-bar-text-color-mode"></a>Настройка строки текстовый режим цвета состояния

Эта определяет специфические для платформы ли текст строки состояния цвета на [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) настраивается в соответствии с яркость панели навигации. Он используется в XAML, задав [ `NavigationPage.StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty) присоединенное свойство в значение [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) перечисления:

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

`NavigationPage.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `NavigationPage.SetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, элементы управления цветом ли текст строки состояния на [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) настраивается в соответствии с яркость панели навигации с [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) перечисления, предоставляя два возможных значения:

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) — Указывает, что состояние цвет текста столбца не должно изменяться.
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) — Указывает, что строки цвет текста состояния должен искать яркость панели навигации.

Кроме того [ `GetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) метод может использоваться для получения текущего значения [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) перечисления, который применяется к [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage).

Результатом является, строке цвета текста и состояния [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) можно изменить в соответствии с яркость панели навигации. В этом примере состояние изменения цвета текста строки как пользователь переключается между [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) и [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) страницы [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

![](ios-images/status-bar-text-color-mode.png "Строка состояния текста цветов режим платформы")

<a name="large_title" />

### <a name="displaying-large-titles"></a>Отображение крупных названий

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

![](ios-images/large-title.png "Чтобы минимизировать эффект от платформы")

<a name="set_status_bar_visibility" />

### <a name="setting-the-status-bar-visibility-on-a-page"></a>Параметр в строке состояния видимости на странице

Этой платформы позволяет настроить видимость для строки состояния на [ `Page` ](xref:Xamarin.Forms.Page), а также возможность управлять как строка состояния или исключается из `Page`. Он используется в XAML, задав `Page.PrefersStatusBarHidden` присоединенное свойство в значение `StatusBarHiddenMode` перечисления и при необходимости `Page.PreferredStatusBarUpdateAnimation` присоединенное свойство в значение `UIStatusBarAnimation` перечисления:

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

`Page.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `Page.SetPrefersStatusBarHidden` Метод в `Xamarin.Forms.PlatformConfiguration.iOSSpecific` пространства имен, используемый для задания видимость строки состояния на [ `Page` ](xref:Xamarin.Forms.Page) , указав одно из `StatusBarHiddenMode` значений перечисления: `Default`, `True` , или `False`. `StatusBarHiddenMode.True` И `StatusBarHiddenMode.False` видимость полосы состояния вне зависимости от ориентации устройства, вы задаете и `StatusBarHiddenMode.Default` значение скрывает строку состояния в среде по вертикали compact.

Результатом является, видимость строки состояния [ `Page` ](xref:Xamarin.Forms.Page) можно задать:

![](ios-images/hide-status-bar.png "Строка состояния видимости специфические для платформы")

> [!NOTE]
> На [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), указанный `StatusBarHiddenMode` значение перечисления также будет обновить строку состояния на все дочерние страницы. Во всех остальных [ `Page` ](xref:Xamarin.Forms.Page)-производные типы, указанного `StatusBarHiddenMode` значение перечисления будут обновлены только строке состояния на текущей странице.

`Page.SetPreferredStatusBarUpdateAnimation` Метод используется для задания как в строке состояния или исключается из [ `Page` ](xref:Xamarin.Forms.Page) , указав одно из `UIStatusBarAnimation` значений перечисления: `None`, `Fade`, или `Slide`. Если `Fade` или `Slide` указано значение перечисления, 0,25 второй анимации выполняются как строка состояния или исключается из `Page`.

<a name="safe_area_layout" />

### <a name="enabling-the-safe-area-layout-guide"></a>Включение направляющая макета безопасной области

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

`Page.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `Page.SetUseSafeArea` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, определяет, включена ли направляющая макета безопасной области.

Результатом является то, что содержимое страницы может располагаться на той части экрана, которое безопасно для всех iPhone:

[![](ios-images/safe-area-layout.png "Направляющая макета безопасной области")](ios-images/safe-area-layout-large.png#lightbox "направляющая макета безопасной области")

> [!NOTE]
> Безопасные области, определяемой Apple, используемый для задания в Xamarin.Forms [ `Page.Padding` ](xref:Xamarin.Forms.Page.Padding) свойство и переопределяет все предыдущие значения этого свойства, которые были установлены.

Безопасной области можно настраивать путем извлечения его [ `Thickness` ](xref:Xamarin.Forms.Thickness) со значением `Page.SafeAreaInsets` метода из [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен. Его можно изменить как требуется и переназначается `Padding` свойства в конструкторе страниц или [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) переопределить:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

<a name="modal-page-presentation-style" />

### <a name="setting-the-modal-page-presentation-style-on-an-ipad"></a>Задание стиля презентации модальные страницы на iPad

Этой платформы используется для задания стиля представления модальные страницы на iPad. Он используется в XAML, задав `Page.ModalPresentationStyle` свойство, используемое для `UIModalPresentationStyle` значение перечисления:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

`Page.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `Page.SetModalPresentationStyle` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используемый для задания стиля модальное презентации на [ `Page` ](xref:Xamarin.Forms.Page) , указав одно из следующих `UIModalPresentationStyle` перечисления значения:

- `FullScreen`, который задает стиль модальное презентации, охватывают весь экран. По умолчанию модальные страницы отображаются при использовании этого стиля представления.
- `FormSheet`, который задает стиль модальное презентации по центру на и меньше, чем экрана.

Кроме того `GetModalPresentationStyle` метод может использоваться для получения текущего значения `UIModalPresentationStyle` перечисления, который применяется к [ `Page` ](xref:Xamarin.Forms.Page).

Результатом является, стиль модальное презентации на [ `Page` ](xref:Xamarin.Forms.Page) можно задать:

[![](ios-images/modal-presentation-style-small.png "Модальное стили презентаций на iPad")](ios-images/modal-presentation-style-large.png#lightbox "модальное стили презентаций на iPad")

> [!NOTE]
> Страниц с использованием этой платформы требуется задать стиль модальные представления необходимо использовать модальной навигации. Дополнительные сведения см. в разделе [модальные страницы Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="layouts"></a>Макеты

В iOS следующие функциональные возможности платформы предоставляется для макеты Xamarin.Forms:

- Управление ли [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) жест касания обрабатывает или передает его на его содержимое. Дополнительные сведения см. в разделе [задержки содержимого штрихи в ScrollView](#delay_content_touches).

<a name="delay_content_touches" />

### <a name="delaying-content-touches-in-a-scrollview"></a>Задержка содержимого штрихи в ScrollView

Таймеру неявное активируется в том случае, когда начинается жест касания в [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) в iOS и `ScrollView` решает, в зависимости от действий пользователя в течение интервала таймера, он должен обрабатывать жест или передайте его на его содержимое. По умолчанию iOS `ScrollView` содержимого штрихи задержки, но это может вызвать проблемы в некоторых случаях при работе с `ScrollView` содержимое, не выиграть жест, когда это требуется. Таким образом, эта определяет специфические для платформы ли `ScrollView` жест касания обрабатывает или передает его на его содержимое. Он используется в XAML, задав `ScrollView.ShouldDelayContentTouches` вложенное свойство, чтобы `boolean` значение:

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

`ScrollView.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `ScrollView.SetShouldDelayContentTouches` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используемый для управления ли [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) жест касания обрабатывает или передает его на его содержимое. Кроме того `SetShouldDelayContentTouches` метод можно использовать для переключения задержки содержимого штрихи, вызвав `ShouldDelayContentTouches` метод для возврата, отложена ли штрихи содержимого:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

В результате [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) можно отключить, задерживая получение содержимого штрихи, таким образом, в этом сценарии [ `Slider` ](xref:Xamarin.Forms.Slider) получает жест, а не [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) странице [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

[![](ios-images/scrollview-delay-content-touches.png "Задержка ScrollView содержимое касается платформы")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView задержки содержимого затрагивает специфические для платформы")

## <a name="application"></a>Приложение

В iOS, предоставляются следующие функциональные возможности платформы для Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application) класса:

- Включение [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) в области прокрутки для записи и совместно использовать жест pan с представления с прокруткой. Дополнительные сведения см. в разделе [Включение одновременных распознавания жестов Pan](#simultaneous-pan-gesture).

<a name="simultaneous-pan-gesture" />

### <a name="enabling-simultaneous-pan-gesture-recognition"></a>Включение распознавания жестов одновременных Pan

Когда [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) прикреплено к представлению внутри прокрутки, все pan, охватывает жесты `PanGestureRecognizer` и не передаются представления с прокруткой. Таким образом больше не прокручивается представления с прокруткой.

Этой платформы позволяет `PanGestureRecognizer` в области прокрутки для записи и совместно использовать жест pan с представления с прокруткой. Он используется в XAML, задав [ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) вложенное свойство, чтобы `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

`Application.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для управления ли распознавателя жестов pan в области прокрутки будет захват жест pan, или записи, так и совместное использование pan жестов с помощью представления с прокруткой. Кроме того [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application})) метод может использоваться для возврата ли жест pan используется совместно с прокруткой представление, содержащее [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer).

Таким образом, с помощью этой платформы включен, когда [ `ListView` ](xref:Xamarin.Forms.ListView) содержит [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer), оба `ListView` и `PanGestureRecognizer` получит жест pan и Обрабатывайте его. Однако при использовании этой платформы отключен, если `ListView` содержит `PanGestureRecognizer`, `PanGestureRecognizer` помещается жест pan и обработать его и `ListView` не будут получать pan жест.

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать iOS особенностей платформы, которые встроены в Xamarin.Forms. Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов.

## <a name="related-links"></a>Связанные ссылки

- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
