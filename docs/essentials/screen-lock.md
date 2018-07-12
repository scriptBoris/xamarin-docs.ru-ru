---
title: 'Xamarin.Essentials: Блокировка экрана'
description: Этот документ описывает класс ScreenLock в Xamarin.Essentials, который можно запросить для предотвращения падает спящем режиме, когда приложение выполняется на экране.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c8110b7abc86fe1d12485579f134997718540e6
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848574"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials: Блокировка экрана

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**ScreenLock** класса можно запросить для предотвращения падает спящем режиме, когда приложение выполняется на экране.

## <a name="using-screenlock"></a>С помощью ScreenLock

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Функциональность блокировки экрана работает путем вызова `RequestActive` и `RequestRelease` методы для запроса на экране отключить.

```csharp
public class ScreenLockTest
{
    public void ToggleScreenLock()
    {
        if (!ScreenLock.IsActive)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease();
    }
}
```

## <a name="api"></a>API

- [Экран блокировки исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Документация по API блокировки экрана](xref:Xamarin.Essentials.ScreenLock)
