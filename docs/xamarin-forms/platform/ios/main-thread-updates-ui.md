---
title: Основной поток управления обновлений на iOS
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать специфические для платформы iOS, позволяющий структурой элементов управления и Подготовка к просмотру обновлений, выполняемых в основном потоке.
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 56109cc9064de4b995e75ceb967abe4995504660
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209549"
---
# <a name="main-thread-control-updates-on-ios"></a>Основной поток управления обновлений на iOS

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Этот iOS платформы позволяет структурой элементов управления и Подготовка к просмотру обновлений, выполняемых в основном потоке, а не выполняется в фоновом потоке. Он понадобится редко, но в некоторых случаях может предотвратить сбои. Его использования в XAML, задав `Application.HandleControlUpdatesOnMainThread` свойство, используемое для `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

`Application.On<iOS>` Метод указывает, что этой платформы будет выполняться только на устройствах iOS. `Application.SetHandleControlUpdatesOnMainThread` Метод в [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) пространства имен, используется для управления ли макет элемента управления и отрисовки обновления выполняются в основном потоке, а не выполняется в фоновом потоке. Кроме того `Application.GetHandleControlUpdatesOnMainThread` метод может использоваться для возврата, которые выполняются ли структура элемента управления и Подготовка к просмотру обновлений в основном потоке.

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
