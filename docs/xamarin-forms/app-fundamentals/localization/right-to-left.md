---
title: Локализация справа налево
description: Локализация справа налево добавлена поддержка направление потока справа налево для приложений Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 67b0d90290b18c7a5b55c5e3496b54970a8cfc38
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617609"
---
# <a name="right-to-left-localization"></a>Локализация справа налево

_Локализация справа налево добавлена поддержка направление потока справа налево для приложений Xamarin.Forms._

> [!NOTE]
> Справа налево локализации требует использования API, 17 или более поздней версии на Android и iOS 9 или более поздней версии.

Направление потока является направление, в котором проверяются элементы пользовательского интерфейса на странице глаза. Некоторые языки, такие как арабский и иврит, требуют, что элементы пользовательского интерфейса располагаются в направлении справа налево. Это можно сделать путем установки [ `VisualElement.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство. Это свойство Возвращает или задает направление, в которую проходят элементы пользовательского интерфейса в родительском элементе, определяющем их размещение и должно быть присвоено одно из [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) значений перечисления:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Установка [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойства [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft) на элементе обычно задает тип выравнивания справа, порядок чтения справа налево и макет элемента управления из справа налево:

[![TodoItemPage на арабском языке с направлением потока справа налево](rtl-images/TodoItemPage-Arabic.png "TodoItemPage на арабском языке с направлением потока справа налево")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage на арабском языке с направлением потока справа налево")

> [!TIP]
> Следует задавать только [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство начального макета. Изменение этого значения во время выполнения приведет это дорого макета процесс, который повлияет на производительность.

Значение по умолчанию [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) значение свойства для элемента без родительского элемента [ `LeftToRight` ](xref:Xamarin.Forms.FlowDirection.LeftToRight), хотя значение по умолчанию `FlowDirection` для элемента с родительским [ `MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Таким образом, элемент наследует `FlowDirection` значение свойства от своего родительского элемента в визуальное дерево и любой элемент можно переопределить значение, он получает от своего родительского элемента.

> [!TIP]
> При локализации приложения для языков справа налево, задать [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство на странице или корневого макета. В результате все элементы, содержащиеся в странице или корневого макета, соответствующим образом реагировать на направление потока.

## <a name="respecting-device-flow-direction"></a>Направление потока уважение устройства

Соблюдение направление потока устройства в зависимости от выбранного языка и региона является выбором явные разработчиков и не происходит автоматически. Это можно сделать, задав [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство на странице, или корневого макета, чтобы `static` [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) значение:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Все дочерние элементы страницы, или корневого макета, будут по умолчанию, наследуют [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) значение.

## <a name="platform-setup"></a>Настройка платформы

Настройка конкретной платформы необходимо включить языковые стандарты справа налево.

### <a name="ios"></a>iOS

Требуется языковой стандарт справа налево необходимо добавить в качестве поддерживаемого языка к элементам массива для `CFBundleLocalizations` в **Info.plist**. В следующем примере показано арабский, были добавлены в массив для `CFBundleLocalizations` ключ:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Языки поддерживаются Info.plist](rtl-images/ios-locales.png "Info.plist поддерживаемые языки")

Дополнительные сведения см. в разделе [основные сведения о локализации в iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

Локализация справа налево, впоследствии можно будет проверить, изменив язык и регион на устройство или симулятор справа налево языковой стандарт, который был указан в **Info.plist**.

> [!WARNING]
> Следует учитывать, что при изменении языка и региона в регион справа налево, в iOS, любое [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) представления будет выдано исключение, если вы не содержат ресурсы, необходимые для языкового стандарта. Например, при тестировании приложения на арабском языке, который имеет `DatePicker`, убедитесь, что **mideast** выбран в **интернационализации** раздел **сборка iOS** области.

### <a name="android"></a>Android

Приложения **AndroidManifest.xml** файла должны обновляться, чтобы `<uses-sdk>` наборов узлов `android:minSdkVersion` атрибут 17 и `<application>` наборов узлов `android:supportsRtl` атрибут `true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

Локализация справа налево впоследствии можно будет проверить, изменив устройстве или эмуляторе для использования языка справа налево или путем включения **Force «справа налево» направление макета** в **параметры > Параметры разработчика**.

### <a name="universal-windows-platform-uwp"></a>Универсальная платформа Windows (UWP)

Необходимые языковые ресурсы должны быть определены в `<Resources>` узел **Package.appxmanifest** файла. В следующем примере показано арабский, были добавлены `<Resources>` узла:

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

Кроме того универсальной платформы Windows требует, что приложения по умолчанию язык и региональные параметры явно определен в библиотеке .NET Standard. Это можно сделать, задав `NeutralResourcesLanguage` атрибут в `AssemblyInfo.cs`, или в другом классе, для культуры по умолчанию:

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

Локализация справа налево, впоследствии можно будет проверить, изменив язык и регион на устройстве соответствующие языковому стандарту, справа налево.

## <a name="limitations"></a>Ограничения

Локализация справа налево Xamarin.Forms в настоящее время имеет ряд ограничений:

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) расположение кнопки панели инструментов элемента расположение и анимации перехода управляется языковой стандарт устройства, а не [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) Направление жеста перетаскивания не выполняет отражение.
- [`Image`](xref:Xamarin.Forms.Image) не выполняет отражение визуального содержимого.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) и [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) ориентации управляется языковой стандарт устройства, а не [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство.
- [`WebView`](xref:Xamarin.Forms.WebView) содержимое не учитывает [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство.
- Объект `TextDirection` свойство должно добавляться, чтобы указать выравнивание текста.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) Ориентация управляется языковой стандарт устройства, а не [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) Выравнивание текста управляется языковой стандарт устройства, а не [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) жестов и выравнивания не будут отменены.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) Ориентация управляется языковой стандарт устройства, а не [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) Размещение управляется языковой стандарт устройства, а не [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство.

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) Выравнивание текста управляется языковой стандарт устройства, а не [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство не наследуется [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) дочерних элементов.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) Выравнивание текста управляется языковой стандарт устройства, а не [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Право на левой языковой поддержки с Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Forms 3.0 справа налево поддержки по [Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>Связанные ссылки

- [TodoLocalizedRTL примера приложения](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
