---
title: 'Xamarin.Essentials: SMS'
description: Класс Sms в Xamarin.Essentials позволяет приложению открыть приложение SMS по умолчанию с указанным сообщением, которое нужно отправить получателю.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: d0655f3e687750e0fca626fb017096a946c0abb3
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898775"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

Класс **Sms** позволяет приложению открыть приложение SMS по умолчанию с указанным сообщением, которое нужно отправить получателю.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

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
