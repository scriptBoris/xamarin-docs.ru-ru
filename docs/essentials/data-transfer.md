---
title: 'Xamarin.Essentials: Передача данных'
description: Класс DataTransfer Xamarin.Essentials позволяет приложениям совместно использовать данные, например текст и веб-ссылки на другие приложения на устройстве.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c1ed298e1317d0a3f78f4dbd9fc89a2b01c6958c
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855114"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: Передача данных

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**DataTransfer** класс позволяет приложениям совместно использовать данные, например текст и веб-ссылки на другие приложения на устройстве.

## <a name="using-data-transfer"></a>С помощью передачи данных

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Функциональные возможности передачи данных работает путем вызова `RequestAsync` метод полезных данных запроса данных, включая сведения для совместного использования для других приложений. Текст и Uri могут сочетаться, и каждая платформа будет обрабатывать фильтрации на основе содержимого.

```csharp

public class DataTransferTest
{
    public async Task ShareText(string text)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

Пользовательский интерфейс для совместного использования на внешнее приложение, которое появляется при запросе:

![Передача данных](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>Различия платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `Subject` свойство используется для требуемой темы сообщения.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` не используется.
* `Title` не используется. 

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `Title` будет, то происходит не задано, если значение по умолчанию имени приложения.
* `Subject` не используется.

-----

## <a name="api"></a>API

- [Исходный код для передачи данных](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Документация по API передачи данных](xref:Xamarin.Essentials.DataTransfer)
