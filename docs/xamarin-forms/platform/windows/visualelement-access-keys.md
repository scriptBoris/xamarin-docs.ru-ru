---
title: Ключи доступа VisualElement на Windows
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать Windows конкретных платформ, указывающее ключ доступа для VisualElement.
ms.prod: xamarin
ms.assetid: 771AF785-76B8-4372-89F5-E4D521D21E0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f14014b257ee5061b6dd074719c3ca27577c6013
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209339"
---
# <a name="visualelement-access-keys-on-windows"></a>Ключи доступа VisualElement на Windows

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Ключи доступа, сочетания клавиш, повышении практичности и доступности приложений на универсальной платформе Windows (UWP), предоставляя интуитивно понятного для пользователей, для быстрого перехода и взаимодействия с приложения видимого пользовательского интерфейса клавиатуры, а не с помощью сенсорного ввода или мышь. Они являются сочетаниями клавишу Alt и один или несколько буквенно-цифровых ключей, обычно нажата последовательно. Сочетания клавиш автоматически поддерживаются для ключей доступа, использующих один буквенно-цифровой символ.

Сочетания клавиш доступа представляют собой перемещаемые эмблемы, отображаемого рядом с элементами управления, которые включают ключи доступа. Каждый совет ключа доступа содержит буквенно-цифровые ключи, активирующих сопоставленного элемента управления. Когда пользователь нажимает клавишу Alt, отображаются сочетания клавиш доступа.

Можно указать ключ доступа для этой универсальной платформы Windows с платформой [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Он используется в XAML, задав [ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) присоединенного свойства, чтобы буквы или цифры и при необходимости задавая [ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) присоединенное свойство в значение [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) перечисления, [ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) вложенное свойство, чтобы `double`и [ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) присоединенное свойство `double`:

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

![Доступ VisualElement разделам платформы](visualelement-access-keys-images/visualelement-accesskeys.png "VisualElement доступ разделам платформы")

При активации пользователем ключ доступа, нажав клавишу Alt, а затем доступ ключа, по умолчанию действия для `VisualElement` будет выполняться. Например, когда пользователь активирует ключ доступа на [ `Switch` ](xref:Xamarin.Forms.Switch), `Switch` переключается. Когда пользователь активирует ключ доступа на [ `Entry` ](xref:Xamarin.Forms.Entry), `Entry` получает фокус. Когда пользователь активирует ключ доступа на [ `Button` ](xref:Xamarin.Forms.Button), обработчик событий для [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) выполнено событие.

Дополнительные сведения о ключах доступа см. в разделе [ключи доступа](/windows/uwp/design/input/access-keys#key-tip-positioning).

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
