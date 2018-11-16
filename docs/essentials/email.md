---
title: 'Xamarin.Essentials: Email'
description: Класс Email в Xamarin.Essentials позволяет приложению открывать приложение электронной почты по умолчанию с указанной информацией, включая тему, текст и получателей (TO, CC, BCC).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c2958cc4572c2f87c46c9edc5fc194284658f24
ms.sourcegitcommit: 704d4cfd418c17b0e85a20c33a16d2419db0be71
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51691754"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: Email

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **Email** позволяет приложению открывать приложение электронной почты по умолчанию с указанной информацией, включая тему, текст и получателей (TO, CC, BCC).

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-email"></a>Использование Email

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Функция Email выполняется путем вызова метода `ComposeAsync` и `EmailMessage`, содержащего сведения о сообщении:

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


## <a name="platform-differences"></a>Различия платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Различия платформ отсутствуют.

# <a name="iostabios"></a>[iOS](#tab/ios)

Различия платформ отсутствуют.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Поддерживается только `PlainText`, так как если `BodyFormat` попытается отправить `Html`, возникнет исключение `FeatureNotSupportedException`.

-----

## <a name="api"></a>API

- [Исходный код Email](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Документация по API Email](xref:Xamarin.Essentials.Email)
