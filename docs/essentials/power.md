---
title: 'Xamarin.Essentials: Состояние экономия энергии питания'
description: Класс Power позволяет программы для получения сведений о состоянии экономия энергии для определения того, если устройство работает в режиме низкого энергопотребления.
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: charlespetzold
ms.author: chape
ms.date: 06/27/2018
ms.openlocfilehash: 6d8ccb5be69eb1ea7ea63d3f5c373d9284089679
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831524"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: Состояние экономия энергии питания

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Power** класс предоставляет сведения о состоянии устройства экономия энергии, который указывает, если устройство работает в режиме низкого энергопотребления. Приложения должны избегать фоновой обработки, если включено состояние устройства экономия энергии.

## <a name="background"></a>Фон

Устройств, работающих на аккумуляторах можно перевести в режим низкого энергопотребления энергии заставки. Иногда устройств меняются местами, в этом режиме автоматически, например, если аккумулятор опускается ниже 20% емкости. Операционная система отвечает на режиме экономии энергии за счет сокращения действия, которые стремятся исчерпаются аккумулятора. Приложения может помочь за счет отключения фоновой обработки или выполнения других действий мощного при включенном режиме экономии энергии.

Для устройств Android **Power** класс возвращает значимую информацию только для Android версии 5.0 (Lollipop) и более поздних версий.

## <a name="using-the-power-class"></a>С помощью класса Power

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Получить текущее состояние устройства, с помощью статического экономия энергии `Power.EnergySaverStatus` свойство:

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

Это свойство возвращает член `EnergySaverStatus` перечисления, равное либо `On`, `Off`, или `Unknown`. Если свойство возвращает `On`, следует избегать приложение фоновой обработки или выполнения других действий, которые могут использовать широкие возможности.

Приложение также следует установить обработчик событий. **Power** класс предоставляет событие, которое активируется при изменении состояния экономия энергии:

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

Если экономия энергии состояние изменится на `On`, приложение должно прекратить выполнение фоновой обработки. Если ее состояние изменяется на `Unknown` или `Off`, приложение могло возобновить фоновой обработки.

## <a name="api"></a>API

- [Код источника питания](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Документация по API Power](xref:Xamarin.Essentials.Power)
