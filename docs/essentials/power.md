---
title: 'Xamarin.Essentials: состояние экономии электроэнергии'
description: Класс Power позволяет программе получить сведения о состоянии экономии электроэнергии, чтобы определить, работает ли устройство в режиме низкого энергопотребления.
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: jamesmontemagno
ms.author: jamont
ms.date: 06/27/2018
ms.openlocfilehash: 96b4aef3a8df571392d43836d46b03b025c80888
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675384"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: состояние экономии электроэнергии

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **Power** предоставляет сведения о состоянии экономии электроэнергии на устройстве, что указывает, работает ли устройство в режиме низкого энергопотребления. Приложениям следует избегать фоновой обработки, если на устройстве включено состояние экономии электроэнергии.

## <a name="background"></a>Фон

Устройства, работающие на аккумуляторах, можно перевести в режим низкого энергопотребления. Иногда устройства переключаются в этот режим автоматически, например, если остаток заряда аккумулятора опускается ниже 20 %. Операционная система в режиме экономии электроэнергии сокращает количество действий, при которых быстро разряжается аккумулятор. В приложениях при включенном режиме экономии не выполняется фоновая обработка и другие энергоемких действий.

Для устройств Android класс **Power** возвращает значимые сведения только для Android версии 5.0 (Lollipop) и более поздних версий.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-the-power-class"></a>Использование класса Power

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Получите сведения о текущем состоянии экономии электроэнергии на устройстве с помощью статического свойства `Power.EnergySaverStatus`:

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

Это свойство возвращает элемент перечисления `EnergySaverStatus`, значением которого является `On`, `Off` или `Unknown`. Если свойство возвращает значение `On`, приложению следует избегать фоновой обработки и выполнения других действий, которые могут потреблять много электроэнергии.

Также приложению следует установить обработчик событий. Класс **Power** предоставляет событие, которое активируется при изменении состояния экономии электроэнергии:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Если состояние экономии электроэнергии изменится на `On`, приложение должно прекратить выполнение фоновой обработки. Если состояние изменится на `Unknown` или `Off`, приложение может возобновить фоновую обработку.

## <a name="api"></a>API

- [Исходный код класса Power](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Документация по API Power](xref:Xamarin.Essentials.Power)
