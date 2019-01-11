---
title: Выбор элемента в iOS
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать специфические для платформы iOS, управляющий, когда происходит выбор элементов в средстве выбора.
ms.prod: xamarin
ms.assetid: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 21c4c289a3fd30db890be6811875412ce4913cf5
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208719"
---
# <a name="picker-item-selection-on-ios"></a>Выбор элемента в iOS

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Этот iOS специфические для платформы контролирует, когда происходит выбор элементов в [ `Picker` ](xref:Xamarin.Forms.Picker), позволяя пользователю указать, что выбор элементов срабатывает при просмотре элементов в элементе управления или только в том случае, когда **сделать** нажата кнопка. Он используется в XAML, задав `Picker.UpdateMode` присоединенное свойство в значение `UpdateMode` перечисления:

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

[![](picker-selection-images/picker-updatemode.png "Средство выбора UpdateMode платформы")](picker-selection-images/picker-updatemode-large.png#lightbox "выбора UpdateMode специфические для платформы")

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
