---
title: 'Xamarin.Essentials: DataTransfer'
description: Класс DataTransfer в Xamarin.Essentials позволяет приложению обмениваться данными, такими как текстовые и веб-ссылки, с другими приложениями на устройстве.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 179d4327aa768e7aa2c81dbbffd694d078327400
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674838"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: DataTransfer

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **DataTransfer** в Xamarin.Essentials позволяет приложению обмениваться данными, такими как текстовые и веб-ссылки, с другими приложениями на устройстве.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-data-transfer"></a>Использование DataTransfer

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Функция DataTransfer выполняется путем вызова метода `RequestAsync` с использованием полезных данных запроса данных, включающих сведения для совместного использования с другими приложениями. Текст и URI могут сочетаться, и каждая платформа будет выполнять фильтрацию по содержимому.

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

Пользовательский интерфейс для общего использования с внешним приложением, отображаемый при запросе:

![Передача данных](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>Различия платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

* Свойство `Subject` используется для требуемой темы сообщения.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` не используется.
* `Title` не используется.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* В `Title` будет использоваться значение по умолчанию (имя приложения), если значение не задано.
* `Subject` не используется.

-----

## <a name="api"></a>API

- [Исходный код класса DataTransfer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Документация по API передачи данных](xref:Xamarin.Essentials.DataTransfer)
