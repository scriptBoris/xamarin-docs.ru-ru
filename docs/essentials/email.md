---
title: 'Xamarin.Essentials: по электронной почте'
description: Класс электронной почты в Xamarin.Essentials позволяет приложения, чтобы открыть приложение электронной почты по умолчанию с указанными сведениями, включая темы, текста и получателей (TO, CC, BCC).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: f113cebfebf4238fd4b75ad8ab248e2abf61efea
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353910"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: по электронной почте

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Электронной почты** класс позволяет приложения, чтобы открыть приложение электронной почты по умолчанию с указанными сведениями, включая темы, текста и получателей (TO, CC, BCC).

## <a name="using-email"></a>С помощью электронной почты

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Функциональные возможности электронной почты работает путем вызова `ComposeAsync` метод `EmailMessage` , содержащий сведения о сообщении:

```csharp
public class EmailTest
{
    public async Task SendEmail(string subject, string body, List<string> recipients)
    {
        try
        {
            var message = new EmailMessage
            {
                Subject = subject,
                Body = body,
                To = recipients,
                //Cc = ccRecipients,
                //Bcc = bccRecipients
            };
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Email is not supported on this device
        }
        catch (Exception ex)
        {
            // Some other exception occurred
        }
    }
}
```

## <a name="api"></a>API

- [Исходный код по электронной почте](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Документация по API электронной почты](xref:Xamarin.Essentials.Email)
