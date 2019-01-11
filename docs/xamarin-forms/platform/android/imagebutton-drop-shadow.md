---
title: ImageButton тени в Android
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать Android конкретных платформ, позволяющий тени на ImageButton.
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 56415aff251149aee01c2e2eb7e335e157180962
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209959"
---
# <a name="imagebutton-drop-shadows-on-android"></a>ImageButton тени в Android

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Это Android платформы используется для включения тени на `ImageButton`. Он используется в XAML, задав `ImageButton.IsShadowEnabled` свойство, используемое для `true`, а также ряд дополнительных необязательно привязываемые свойства, которые управляют тени:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
       <ImageButton ...
                    Source="XamarinLogo.png"
                    BackgroundColor="GhostWhite"
                    android:ImageButton.IsShadowEnabled="true"
                    android:ImageButton.ShadowColor="Gray"
                    android:ImageButton.ShadowRadius="12">
            <android:ImageButton.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </android:ImageButton.ShadowOffset>
        </ImageButton>
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var imageButton = new Xamarin.Forms.ImageButton { Source = "XamarinLogo.png", BackgroundColor = Color.GhostWhite, ... };
imageButton.On<Android>()
           .SetIsShadowEnabled(true)
           .SetShadowColor(Color.Gray)
           .SetShadowOffset(new Size(10, 10))
           .SetShadowRadius(12);
```

> [!IMPORTANT]
> Тень рисуется как часть `ImageButton` фона и фона только рисуется Если `BackgroundColor` свойству. Таким образом, тень не рисуется Если `ImageButton.BackgroundColor` свойства не задано.

`ImageButton.On<Android>` Метод указывает, что этой платформы будет выполняться только в Android. `ImageButton.SetIsShadowEnabled` Метод в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) пространства имен, используется для управления отвечает за включение тени на `ImageButton`. Кроме того можно вызвать следующие методы для управления тени:

- `SetShadowColor` — Задает цвет тени. Цвет по умолчанию [ `Color.Default` ](xref:Xamarin.Forms.Color.Default*).
- `SetShadowOffset` — Задает смещение тени. Смещение меняет направление тени приводится и указываются в виде [ `Size` ](xref:Xamarin.Forms.Size) значение. `Size` Структуры значения выражаются в аппаратно независимых единицах, первое значение, что расстояние до (отрицательное значение) или влево (положительное значение), а второе значение, что расстояние выше (отрицательное значение) или ниже (положительное значение) . Значение этого свойства по умолчанию — (0.0, 0.0), тень, что приводит к приведение вокруг каждая сторона `ImageButton`.
- `SetShadowRadius`— Задает радиуса размытия, используемую для отрисовки тени. Значение радиуса по умолчанию — 10.0.

> [!NOTE]
> Состояние тени можно запрашивать путем вызова `GetIsShadowEnabled`, `GetShadowColor`, `GetShadowOffset`, и `GetShadowRadius` методы.

Результатом является то, что тень можно включить на `ImageButton`:

![](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png "ImageButton с тенью")

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
