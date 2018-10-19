---
title: Особенности платформы Windows
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье показано, как использовать Windows особенностей платформы, которые встроены в Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 2130bbbedbab66fac9427947ca42f21c346360ce
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "38998420"
---
# <a name="windows-platform-specifics"></a>Особенности платформы Windows

_Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье показано, как использовать Windows особенностей платформы, которые встроены в Xamarin.Forms._

## <a name="visualelements"></a>VisualElements

На универсальной платформе Windows следующие функциональные возможности платформы предоставляется для представления, страницы и макеты Xamarin.Forms:

- Установка ключа доступа для [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [задание VisualElement сочетаний клавиш](#visualelement-accesskeys).
- Отключение режима устаревших цвет в поддерживаемой [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [отключение цветовой режим прежних версий](#legacy-color-mode).

<a name="visualelement-accesskeys" />

### <a name="setting-visualelement-access-keys"></a>Задание VisualElement сочетаний клавиш

Ключи доступа, сочетания клавиш, повышении практичности и доступности приложений на универсальной платформе Windows, предоставляя интуитивно понятного для пользователей, для быстрого перехода и взаимодействия с приложения видимого пользовательского интерфейса клавиатуры, а не с помощью сенсорного ввода или При нажатии мышью. Они являются сочетаниями клавишу Alt и один или несколько буквенно-цифровых ключей, обычно нажата последовательно. Сочетания клавиш автоматически поддерживаются для ключей доступа, использующих один буквенно-цифровой символ.

Сочетания клавиш доступа представляют собой перемещаемые эмблемы, отображаемого рядом с элементами управления, которые включают ключи доступа. Каждый совет ключа доступа содержит буквенно-цифровые ключи, активирующих сопоставленного элемента управления. Когда пользователь нажимает клавишу Alt, отображаются сочетания клавиш доступа.

Можно указать ключ доступа для этой платформы [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Он используется в XAML, задав [ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) присоединенного свойства, чтобы буквы или цифры и при необходимости задавая [ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) присоединенное свойство в значение [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) перечисления, [ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) вложенное свойство, чтобы `double`и [ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) присоединенное свойство `double`:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

`VisualElement.On<Windows>` Метод указывает, что этой платформы будет выполняться только на универсальной платформе Windows. [ `VisualElement.SetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.String)) Метод в [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) пространства имен, используемый для задания значения ключа доступа для `VisualElement`. [ `VisualElement.SetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},Xamarin.Forms.AccessKeyPlacement)) Метод, при необходимости указывает позицию будет использоваться для отображения ключевых подсказок доступ, с помощью [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) перечисления, предоставляя следующие возможные значения:

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) — Указывает, что размещение ключевых подсказок доступ будет определяться операционной системой.
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) — Указывает, что ключевых подсказок доступа будет отображаться над верхним краем `VisualElement`.
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) — Указывает, что ключевых подсказок доступа отображается под нижней границе `VisualElement`.
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) — Указывает, что ключевых подсказок доступа будет отображаться справа от правого края `VisualElement`.
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) — Указывает, что ключевых подсказок доступа будет отображаться слева от левого края `VisualElement`.
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) — Указывает, что ключевых подсказок доступа будет отображаться перекрывающимся по центру `VisualElement`.

> [!NOTE]
> Как правило [ `Auto` ](xref:Xamarin.Forms.AccessKeyPlacement.Auto) размещения ключевых подсказок достаточно, который включает в себя поддержку адаптивной пользовательских интерфейсов.

[ `VisualElement.SetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) И [ `VisualElement.SetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) методы могут использоваться для более детального контроля доступа расположения ключевых подсказок. Аргумент `SetAccessKeyHorizontalOffset` метод указывает далеко перемещение доступа ключевых подсказок влево или вправо, а аргумент `SetAccessKeyVerticalOffset` метод показывает, как далеко необходимо переместить ключевых подсказок доступа вверх или вниз.

>[!NOTE]
> Смещения ключевых подсказок доступа невозможно установить, если задать расположение ключа доступа `Auto`.

Кроме того [ `GetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), и [ `GetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) можно использовать методы Для получения доступа значение и его расположение ключа.

Результатом является то, что сочетания клавиш доступа могут отображаться рядом с любой [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) экземплярам, которые определяют доступ к ключам, нажав клавишу Alt:

![Доступ VisualElement разделам платформы](windows-images/visualelement-accesskeys.png "VisualElement доступ разделам платформы")

При активации пользователем ключ доступа, нажав клавишу Alt, а затем доступ ключа, по умолчанию действия для `VisualElement` будет выполняться. Например, когда пользователь активирует ключ доступа на [ `Switch` ](xref:Xamarin.Forms.Switch), `Switch` переключается. Когда пользователь активирует ключ доступа на [ `Entry` ](xref:Xamarin.Forms.Entry), `Entry` получает фокус. Когда пользователь активирует ключ доступа на [ `Button` ](xref:Xamarin.Forms.Button), обработчик событий для [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) выполнено событие.

Дополнительные сведения о ключах доступа см. в разделе [ключи доступа](/windows/uwp/design/input/access-keys#key-tip-positioning).

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>Отключение цветовой режим прежних версий

Некоторые представления Xamarin.Forms признаков устаревших цветовой режим. В этом режиме при [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) представления свойству `false`, представление будет переопределять цвета, установленные пользователем с помощью собственного цветов по умолчанию для отключенного состояния. Для обеспечения обратной совместимости, этот режим прежних версий цвет остается поведение по умолчанию для поддерживаемых представлений.

Этой платформы отключает этот режим прежних версий цвета, таким образом, чтобы настроить в представлении пользователем цвета остаются даже в том случае, если представление отключено. Он используется в XAML, задав [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) вложенное свойство, чтобы `false`:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Editor Text="Enter text here"
                TextColor="Blue"
                BackgroundColor="Bisque"
                windows:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Windows>` Метод указывает, что этой платформы будет выполняться только в Windows. [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) пространства имен, используется для управления ли устаревших цветовой режим отключен. Кроме того [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) метод может использоваться для возврата, не отключен ли устаревших цветовой режим.

Результатом является, что устаревшие цветовой режим можно отключить, таким образом, чтобы настроить в представлении пользователем цвета остаются даже при отключении представления:

![](windows-images/legacy-color-mode-disabled.png "Устаревшие цветовой режим отключен")

> [!NOTE]
> При задании [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) для представления, устаревшие цветовой режим полностью игнорируется. Дополнительные сведения о визуальных состояниях см. в разделе [Xamarin.Forms Диспетчер визуальных состояний](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="views"></a>Представления

В универсальной платформы Windows (UWP), предоставляются следующие функциональные возможности платформы для представления Xamarin.Forms:

- Обнаружение, порядок чтения из текстового содержимого в [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), и [ `Label` ](xref:Xamarin.Forms.Label) экземпляров. Дополнительные сведения см. в разделе [обнаружение порядок чтения из содержимого](#inputview-readingorder).
- Включение поддержки жест касания в [ `ListView` ](xref:Xamarin.Forms.ListView). Дополнительные сведения см. в разделе [Включение коснитесь поддержки жестов в ListView](#listview-selectionmode).
- Включение [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) для взаимодействия с подсистемой проверки орфографии. Дополнительные сведения см. в разделе [Включение проверки орфографии SearchBar](#searchbar-spellcheck).
- Включение [ `WebView` ](xref:Xamarin.Forms.WebView) на отображение оповещений JavaScript в диалоговом окне сообщения универсальной платформы Windows. Дополнительные сведения см. в разделе [отображения предупреждения JavaScript](#webview-javascript-alert).

<a name="inputview-readingorder" />

### <a name="detecting-reading-order-from-content"></a>Обнаружение, порядок чтения из содержимого

Этой платформы позволяет порядок чтения (слева направо или справа налево) двунаправленного текста в [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), и [ `Label` ](xref:Xamarin.Forms.Label) экземпляры, чтобы быть обнаруженным динамически. Он используется в XAML, задав [ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (для `Entry` и `Editor` экземпляры) или [ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) вложенное свойство, чтобы `boolean` значение:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

`Editor.On<Windows>` Метод указывает, что этой платформы будет выполняться только на универсальной платформе Windows. [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) пространства имен, используется для управления ли порядок чтения определяется из содержимого [ `InputView` ](xref:Xamarin.Forms.InputView). Кроме того `InputView.SetDetectReadingOrderFromContent` метод может использоваться для переключения между режимами порядок чтения определяется из содержимого путем вызова [ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView})) метод для возврата текущего значения:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

В результате [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), и [ `Label` ](xref:Xamarin.Forms.Label) экземпляров могут быть порядок чтения их содержимое, динамически обнаружены:

[![Обнаружение порядок чтения из содержимого платформы InputView](windows-images/editor-readingorder.png "InputView, обнаружение порядок чтения из содержимого платформы")](windows-images/editor-readingorder-large.png#lightbox "InputView, обнаружение порядок чтения из содержимое конкретной платформы")

> [!NOTE]
> В отличие от параметра [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство, логика для представлений, которые позволяют обнаружить порядок чтения из их текстовое содержимое не повлияет на способ выравнивания текста в представлении. Вместо этого он корректирует порядок, в котором расположены блоки двунаправленного текста.

<a name="listview-selectionmode" />

### <a name="enabling-tap-gesture-support-in-a-listview"></a>Включение поддержки жест касания в ListView

На универсальной платформе Windows, по умолчанию Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView) использует собственные `ItemClick` событий реагировать на взаимодействие, а не собственного `Tapped` событий. Она предоставляет возможности, специальных возможностей, позволяющих работать с Windows экранного диктора и клавиатуры `ListView`. Тем не менее, но также и отображает все жесты касания внутри `ListView` вышел из строя.

Эта определяет специфические для платформы ли элементы в [ `ListView` ](xref:Xamarin.Forms.ListView) может отвечать на коснитесь жестов и, следовательно ли собственный `ListView` активируется `ItemClick` или `Tapped` событий. Он используется в XAML, задав [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) присоединенное свойство в значение [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) перечисления:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

`ListView.On<Windows>` Метод указывает, что этой платформы будет выполняться только на универсальной платформе Windows. [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)) Метод в [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) пространства имен, используемый для управления ли элементы в [ `ListView` ](xref:Xamarin.Forms.ListView) может отвечать на коснитесь жесты, с [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) перечисления, предоставляя два возможных значения:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) — Указывает, что `ListView` запустит собственный `ItemClick` событий для обработки взаимодействия, а следовательно предоставляют функции специальных возможностей. Таким образом, Экранный диктор Windows и клавиатуры может взаимодействовать с `ListView`. Тем не менее, элементы в `ListView` не может отвечать на коснитесь жестов. Это поведение по умолчанию для `ListView` экземпляров на универсальной платформе Windows.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) — Указывает, что `ListView` запустит собственный `Tapped` событий для обработки взаимодействия. Таким образом, элементы в `ListView` может отвечать на коснитесь жестов. Тем не менее, нет никаких функций специальных возможностей, и поэтому экранного диктора Windows и клавиатуры не может взаимодействовать с `ListView`.

> [!NOTE]
> `Accessible` И `Inaccessible` режимы выбора являются взаимоисключающими, и необходимо будет выбрать доступный [ `ListView` ](xref:Xamarin.Forms.ListView) или `ListView` , может отвечать на коснитесь жестов.

Кроме того [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView})) метод может использоваться для возврата текущего [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode).

Результат является то, что указанный [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) применяется к [ `ListView` ](xref:Xamarin.Forms.ListView), какие элементы управления ли элементы в `ListView` может отвечать на коснитесь жестов и, следовательно ли собственный `ListView` активируется `ItemClick` или `Tapped` событий.

<a name="searchbar-spellcheck" />

### <a name="enabling-searchbar-spell-check"></a>Включение проверки орфографии SearchBar

Этой платформы позволяет [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) для взаимодействия с подсистемой проверки орфографии. Он используется в XAML, задав [ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) вложенное свойство, чтобы `boolean` значение:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

`SearchBar.On<Windows>` Метод указывает, что этой платформы будет выполняться только на универсальной платформе Windows. [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) пространства имен, включает средство проверки орфографии и выключает. Кроме того `SearchBar.SetIsSpellCheckEnabled` метод может использоваться для включения проверки орфографии, вызвав [ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) метод для возврата, включен ли средство проверки орфографии:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

Результатом является этот текст, введенный в [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) можно проверять орфографию, с помощью неправильное написание указан для пользователя:

![SearchBar орфографии проверки платформы](windows-images/searchbar-spellcheck.png "SearchBar орфографии проверка платформы")

> [!NOTE]
> `SearchBar` В класс [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) пространство имен также содержит [ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) и [ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) методы, которые могут использоваться для включения и отключения Средство проверки орфографии на `SearchBar`, соответственно.

<a name="webview-javascript-alert" />

### <a name="displaying-javascript-alerts"></a>Отображение оповещений на JavaScript

Этой платформы позволяет [ `WebView` ](xref:Xamarin.Forms.WebView) на отображение оповещений JavaScript в диалоговом окне сообщения универсальной платформы Windows. Он используется в XAML, задав [ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) вложенное свойство, чтобы `boolean` значение:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var webView = new Xamarin.Forms.WebView
{
  Source = new HtmlWebViewSource
  {
    Html = @"<html><body><button onclick=""window.alert('Hello World from JavaScript');"">Click Me</button></body></html>"
  }
};
webView.On<Windows>().SetIsJavaScriptAlertEnabled(true);
```

`WebView.On<Windows>` Метод указывает, что этой платформы будет выполняться только на универсальной платформе Windows. [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) пространства имен, используется для управления предупреждения JavaScript, включены ли. Кроме того `WebView.SetIsJavaScriptAlertEnabled` метод может использоваться для включения предупреждения JavaScript путем вызова [ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) метод для возврата, включены ли они:

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

Результатом является то, что предупреждения JavaScript могут отображаться в диалоговом окне сообщения универсальной платформы Windows:

![Предупреждение кода JavaScript платформы](windows-images/webview-javascript-alert.png "предупреждение кода JavaScript платформы")

## <a name="pages"></a>Pages

На универсальной платформе Windows следующие функциональные возможности платформы предоставляется для страницы Xamarin.Forms:

- Свертывание [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) панели навигации. Дополнительные сведения см. в разделе [свертывание панель навигации MasterDetailPage](#collapsable_navigation_bar).
- Настройка параметров размещения панели инструментов. Дополнительные сведения см. в разделе [изменения расположения панели инструментов страницы](#toolbar_placement).
- Включение значков страниц, отображаемых на [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) панели инструментов. Дополнительные сведения см. в разделе [включения значков на TabbedPage](#tabbedpage-icons).

<a name="collapsable_navigation_bar" />

### <a name="collapsing-a-masterdetailpage-navigation-bar"></a>Свертывание MasterDetailPage панель навигации

Этой платформы позволяет свернуть панель навигации на [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)и используется в XAML, задав [ `MasterDetailPage.CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty) и [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty)присоединенных свойств:

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

`MasterDetailPage.On<Windows>` Метод указывает, что этой платформы будет выполняться только в Windows. [ `Page.SetCollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle)) Метод в [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) пространства имен, используется для указания стиль свернуть с [ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) перечисления, предоставляя два значения: [ `Full` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full) и [ `Partial` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial). [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},System.Double)) Метод позволяет задать ширину частично свернутой областью навигации.

Результатом является то, что указанный [ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) применяется к [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) экземпляра с шириной также указан:

[![](windows-images/collapsed-navigation-bar.png "Свернуть панель навигации с платформой")](windows-images/collapsed-navigation-bar-large.png#lightbox "свернуть панель навигации с платформой")

<a name="toolbar_placement" />

### <a name="changing-the-page-toolbar-placement"></a>Изменение панели инструментов страницы размещения

Этой платформы можно изменять расположение панели инструментов на [ `Page` ](xref:Xamarin.Forms.Page)и используется в XAML, задав [ `Page.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty) присоединенное свойство в значение [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) перечисления:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

`Page.On<Windows>` Метод указывает, что этой платформы будет выполняться только в Windows. [ `Page.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement)) Метод в [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) пространства имен, используемый для задания размещения панели инструментов, с помощью [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) предоставляя перечисления три значения: [ `Default` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default), [ `Top` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top), и [ `Bottom` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom).

Результатом является размещение выбранной панели инструментов назначается [ `Page` ](xref:Xamarin.Forms.Page) экземпляр:

[![](windows-images/toolbar-placement.png "Панель инструментов размещения платформы")](windows-images/toolbar-placement-large.png#lightbox "платформы размещения панели инструментов")

<a name="tabbedpage-icons" />

### <a name="enabling-icons-on-a-tabbedpage"></a>Включение значков на TabbedPage

Этой платформы позволяет значков страниц, отображаемых на [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) инструментов и предоставляет возможность при необходимости укажите размер значка. Он используется в XAML, задав [ `TabbedPage.HeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) вложенное свойство, чтобы `true`и при необходимости задавая [ `TabbedPage.HeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) вложенное свойство, чтобы [ `Size` ](xref:Xamarin.Forms.Size) значение:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:TabbedPage.HeaderIconsEnabled="true">
    <windows:TabbedPage.HeaderIconsSize>
        <Size>
            <x:Arguments>
                <x:Double>24</x:Double>
                <x:Double>24</x:Double>
            </x:Arguments>
        </Size>
    </windows:TabbedPage.HeaderIconsSize>
    <ContentPage Title="Todo" Icon="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" Icon="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" Icon="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

public class WindowsTabbedPageIconsCS : Xamarin.Forms.TabbedPage
{
  public WindowsTabbedPageIconsCS()
    {
    On<Windows>().SetHeaderIconsEnabled(true);
    On<Windows>().SetHeaderIconsSize(new Size(24, 24));

    Children.Add(new ContentPage { Title = "Todo", Icon = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", Icon = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", Icon = "contacts.png" });
  }
}
```

`TabbedPage.On<Windows>` Метод указывает, что этой платформы будет выполняться только на универсальной платформе Windows. [ `TabbedPage.SetHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},System.Boolean)) Метод в [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) используется пространство имен, включить или отключить значки заголовка. [ `TabbedPage.SetHeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsSize(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},Xamarin.Forms.Size)) Метод при необходимости указывает размер значка заголовка с [ `Size` ](xref:Xamarin.Forms.Size) значение.

Кроме того `TabbedPage` в класс `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` пространство имен также содержит [ `EnableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*) метод, позволяющий значки заголовка [ `DisableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*) метод, который отключает значки заголовка и [ `IsHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*) метод, возвращающий `boolean` значение, указывающее, включены ли значки заголовка.

Результатом является страницы могут отображаться значки [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) панель инструментов с размер значка, при необходимости задать требуемый размер:

![TabbedPage значков с поддержкой платформы](windows-images/tabbedpage-icons.png "TabbedPage значков с поддержкой платформы")

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать Windows особенностей платформы, которые встроены в Xamarin.Forms. Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов.

## <a name="related-links"></a>Связанные ссылки

- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
