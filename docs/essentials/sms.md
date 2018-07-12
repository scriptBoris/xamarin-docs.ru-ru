---
title: 'Xamarin.Essentials: SMS'
description: Класс Sms в Xamarin.Essentials позволяет приложения, чтобы открыть приложение SMS по умолчанию с заданным сообщением для отправки получателю.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a93a67b83ea8f435a5e3ad5d26e1d6cbbb7092f7
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815601"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Sms** класс позволяет приложения, чтобы открыть приложение SMS по умолчанию с заданным сообщением для отправки получателю.

## <a name="using-sms"></a>С помощью Sms

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Функциональность SMS работает путем вызова `ComposeAsync` метод `SmsMessage` , содержащий получателя сообщения и текст сообщения, оба из которых являются необязательными.

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, recipient);
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Исходный код SMS](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Документация по SMS API](xref:Xamarin.Essentials.Sms)
