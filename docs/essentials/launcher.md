---
title: Средство запуска Xamarin.Essentials
description: Класс запуска в Xamarin.Essentials позволяет приложения, чтобы открыть URI в системе.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 3ab820ece127558c1a5ca8b13c05fc4d6f20646e
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353955"
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
    public async Task OpenRideShare()
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
