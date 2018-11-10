---
title: 'Xamarin.Essentials: ScreenLock'
description: В этом документе описывается класс ScreenLock в Xamarin.Essentials, который может запрашивать сохранение активного режима работы экрана при работе приложения.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3bf8c949650cf9f039a5a516366a90e717dc944b
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675319"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials: ScreenLock

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **ScreenLock** может запрашивать сохранение активного режима работы экрана при работе приложения.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-screenlock"></a>Использование ScreenLock

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Функция ScreenLock выполняется путем вызова методов `RequestActive` и `RequestRelease` для запроса на отмену выключения экрана.

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

- [Исходный код ScreenLock](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Документация по API ScreenLock](xref:Xamarin.Essentials.ScreenLock)
