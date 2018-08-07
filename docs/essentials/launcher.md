---
title: Средство запуска Xamarin.Essentials
description: Класс запуска в Xamarin.Essentials позволяет приложения, чтобы открыть URI в системе.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 252bb873c1494265aafb2285057490ca29ce7419
ms.sourcegitcommit: bf51592be39b2ae3d63d029be1d7745ee63b0ce1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39573637"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: средство запуска

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Запуска** класс позволяет приложения, чтобы открыть URI в системе. Это часто используется при глубокой связи в другое приложение пользовательские схемы URI. Если вам нужно открыть в браузере веб-сайта, а затем обратитесь к **[браузера](open-browser.md)** API.

## <a name="using-launcher"></a>С помощью средства запуска

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Выполнить вызов функции запуска `OpenAsync` метод и передать `string` или `Uri` для открытия. При необходимости `CanOpenAsync` метод может использоваться для проверки, если схема URI могут обрабатываться приложением на устройстве.

```csharp
public class LauncherTest
{
    public async Task OpenRideShareAsync()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

## <a name="api"></a>API

- [Средство запуска исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Документация по API средства запуска](xref:Xamarin.Essentials.Launcher)
