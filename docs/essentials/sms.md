---
title: 'Xamarin.Essentials: SMS'
description: Класс Sms в Xamarin.Essentials позволяет приложению открыть приложение SMS по умолчанию с указанным сообщением, которое нужно отправить получателю.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: fa55a17e99a11861b98c4d515df882ed3af58a0b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102741"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **Sms** позволяет приложению открыть приложение SMS по умолчанию с указанным сообщением, которое нужно отправить получателю.

## <a name="using-sms"></a>Использование класса Sms

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Для отправки SMS нужно вызвать метод `ComposeAsync` класса `SmsMessage`, передав получателя и тело сообщения. Оба параметра являются необязательными.

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

Дополнительно в метод `SmsMessage` можно передать несколько получателей:

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string[] recipients)
    {
        try
        {
            var message = new SmsMessage(messageText, recipients);
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

- [Исходный код Sms](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms).
- [Документация по API Sms](xref:Xamarin.Essentials.Sms).
