---
title: ListView SelectionMode в Windows
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать Windows конкретных платформ, управляет ли элементы в ListView может отвечать на коснитесь жестов.
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: d2a94347448af031f50341729d77c7385225d107
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209589"
---
# <a name="listview-selectionmode-on-windows"></a>ListView SelectionMode в Windows

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

На универсальной платформе Windows, по умолчанию Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView) использует собственные `ItemClick` событий реагировать на взаимодействие, а не собственного `Tapped` событий. Она предоставляет возможности, специальных возможностей, позволяющих работать с Windows экранного диктора и клавиатуры `ListView`. Тем не менее, но также и отображает все жесты касания внутри `ListView` вышел из строя.

Эта определяет специфические для платформы, универсальной платформы Windows ли элементы в [ `ListView` ](xref:Xamarin.Forms.ListView) может отвечать на коснитесь жестов и, следовательно ли собственный `ListView` активируется `ItemClick` или `Tapped` событий. Он используется в XAML, задав [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) присоединенное свойство в значение [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) перечисления:

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

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
