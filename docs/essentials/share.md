---
title: 'Xamarin.Essentials: класс Share'
description: Класс Share в Xamarin.Essentials позволяет приложению обмениваться данными, такими как текст и веб-ссылки, с другими приложениями на устройстве.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 6fd3fd90d1e2ada225dafdd855f8903688677660
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899377"
---
# <a name="xamarinessentials-share"></a>Xamarin.Essentials: класс Share

Класс **Share** позволяет приложению обмениваться данными, такими как текст и веб-ссылки, с другими приложениями на устройстве.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-share"></a>Использование класса Share

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Чтобы использовать функции класса Share, вызовите метод `RequestAsync` с полезными данными запроса, включающего сведения для обмена с другими приложениями. Текст и URI могут сочетаться, и каждая платформа будет выполнять фильтрацию по содержимому.

```csharp

public class ShareTest
{
    public async Task ShareText(string text)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

Пользовательский интерфейс для общего использования с внешним приложением, отображаемый при запросе:

![Общий доступ](share-images/share.png)

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

- [Исходный код Share](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Share)
- [Документация по API для Share](xref:Xamarin.Essentials.Share)
