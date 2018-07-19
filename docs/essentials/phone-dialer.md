---
title: 'Xamarin.Essentials: Телефон'
description: Класс PhoneDialer в Xamarin.Essentials позволяет приложения, чтобы открыть в программе номер телефона
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 34a6c80836d8cb42b1f8fd95718fe248d4701c0f
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130797"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Телефон

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**PhoneDialer** класс позволяет приложения, чтобы открыть номер телефона в программе.

## <a name="using-phone-dialer"></a>С помощью телефона

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Функциональность телефон работает путем вызова `Open` метод с номером телефона, чтобы открыть номеронабирателя с. Когда `Open` запрашивается API автоматически будет пытаться форматировать количество в зависимости от кода страны, если указано.

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

- [Телефон исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Документация по API набирателя номера телефона](xref:Xamarin.Essentials.PhoneDialer)
