---
title: 'Xamarin.Essentials: Состояние экономия энергии'
description: Класс Power позволяет программе получить состояние экономия энергии, чтобы определить, если устройство работает в режим низкого энергопотребления.
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: charlespetzold
ms.author: chape
ms.date: 06/27/2018
ms.openlocfilehash: 6d8ccb5be69eb1ea7ea63d3f5c373d9284089679
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080570"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: Состояние экономия энергии

![Предварительная версия NuGet](~/media/shared/pre-release.png)

**Power** класс предоставляет сведения о состоянии экономия энергии устройства, которое указывает, если устройство работает в режим низкого энергопотребления. Приложения должны избегать фоновой обработки, если включено состояние устройства экономия энергии.

## <a name="background"></a>Фон

Устройства под управлением от батарей можно перевести в режим низкого потребления энергии заставки. Иногда устройств переключаются в этот режим автоматически, например, если аккумулятор становится меньше 20% мощности. Операционная система отвечает на режим Экономия энергии за счет уменьшения действий, которые, как правило, к быстрому расходу аккумулятора. Приложения могут помочь, чтобы избежать фоновой обработки или других действий, мощного при включенном режиме экономия энергии.

Для устройств Android **Power** класса возвращает значимую информацию только для Android версии 5.0 (без описания операций) и более поздних версий.

## <a name="using-the-power-class"></a>С помощью класса питания

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Получить текущее состояние экономия энергии устройства с помощью статического `Power.EnergySaverStatus` свойство:

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

Это свойство возвращает член `EnergySaverStatus` перечисления, который является либо `On`, `Off`, или `Unknown`. Если свойство возвращает `On`, следует избегать приложение фоновой обработки или других действий, которые могут потреблять большое количество электроэнергии.

Приложение также следует установить обработчик события. **Power** класс предоставляет событие, которое вызывается при изменении состояния экономия энергии:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanaged += OnEnergySaverStatusChanaged;
    }

    private void OnEnergySaverStatusChanaged(EnergySaverStatusChanagedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

При изменении состояния экономия энергии для `On`, приложение должно прекратить выполнение фоновой обработки. Если состояние меняется на `Unknown` или `Off`, приложение может возобновить фоновой обработки.

## <a name="api"></a>API

- [Код источника питания](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Документация по API](xref:Xamarin.Essentials.Power)
