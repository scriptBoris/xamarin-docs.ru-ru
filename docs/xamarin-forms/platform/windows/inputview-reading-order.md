---
title: Порядок чтения InputView на Windows
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать Windows конкретных платформ, позволяющий порядок чтения двунаправленного текста быть обнаруженным динамически.
ms.prod: xamarin
ms.assetid: E61BAEE0-C8B7-4F33-8DDC-FA1B9CA8E81D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f926425a3d2e2fb6494f42e962bc5f2fa1ba3b1c
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208849"
---
# <a name="inputview-reading-order-on-windows"></a>Порядок чтения InputView на Windows

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Это универсальная платформа Windows платформы позволяет порядок чтения (слева направо или справа налево) двунаправленного текста в [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor)и [ `Label` ](xref:Xamarin.Forms.Label) экземпляры, чтобы быть обнаруженным динамически. Он используется в XAML, задав [ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (для `Entry` и `Editor` экземпляры) или [ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) вложенное свойство, чтобы `boolean` значение:

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

[![Обнаружение порядок чтения из содержимого платформы InputView](inputview-reading-order-images/editor-readingorder.png "InputView, обнаружение порядок чтения из содержимого платформы")](inputview-reading-order-images/editor-readingorder-large.png#lightbox "InputView, обнаружение порядок чтения из содержимое конкретной платформы")

> [!NOTE]
> В отличие от параметра [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) свойство, логика для представлений, которые позволяют обнаружить порядок чтения из их текстовое содержимое не повлияет на способ выравнивания текста в представлении. Вместо этого он корректирует порядок, в котором расположены блоки двунаправленного текста.

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
