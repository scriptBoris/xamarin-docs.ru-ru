---
title: 'Xamarin.Essentials: PhoneDialer'
description: Класс PhoneDialer в Xamarin.Essentials позволяет приложению открывать номер телефона в набирателе номера
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: bccc76e1602c475a5e4cc9a95d498d11f9a379b1
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675423"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: PhoneDialer

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **PhoneDialer** позволяет приложению открывать номер телефона в набирателе номера.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>Использование PhoneDialer

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Функция PhoneDialer выполняется путем вызова метода `Open` с использованием номера телефона, который нужно открыть в набирателе номера. При запросе команды `Open` API будет автоматически пытаться отформатировать номер на основе кода страны, если он указан.

```csharp
public class PhoneDialerTest
{
    public async Task PlacePhoneCall(string number)
    {
        try
        {
            PhoneDialer.Open(number);
        }
        catch (ArgumentNullException anEx)
        {
            // Number was null or white space
        }
        catch (FeatureNotSupportedException ex)
        {
            // Phone Dialer is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Исходный код PhoneDialer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Документация по API PhoneDialer](xref:Xamarin.Essentials.PhoneDialer)
