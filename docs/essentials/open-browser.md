---
title: 'Xamarin.Essentials: открытие браузера'
description: Класс Browser в Xamarin.Essentials позволяет приложению открывать веб-ссылку в предпочитаемом браузере оптимизированной системы или внешнем браузере.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: ea2a10c11a77fcb2b3ce142d176522ebf0310725
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898905"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: браузер

Класс **Browser** позволяет приложению открыть веб-ссылку в предпочитаемом браузере оптимизированной системы или внешнем браузере.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-browser"></a>Использование класса Browser

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Чтобы использовать функции класса Browser, вызовите метод `OpenAsync` с параметрами `Uri` и `BrowserLaunchMode`.

```csharp

public class BrowserTest
{
    public async Task<bool> OpenBrowser(Uri uri)
    {
        return await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

Этот метод возвращает значение при _запуске_ (и не обязательно _закрытии_) браузера пользователем.  Результат `bool` показывает, был ли запуск успешным.

## <a name="platform-implementation-specifics"></a>Особенности реализации для платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Режим запуска определяет, как запускается браузер:

## <a name="system-preferred"></a>Предпочитаемый режим системы

С помощью [пользовательских вкладок Chrome](https://developer.chrome.com/multidevice/android/customtabs) будет выполнена попытка загрузить Uri и поддерживать навигацию.

## <a name="external"></a>Внешняя

С помощью `Intent` будет запрошено открытие Uri в обычном системном браузере.

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Предпочитаемый режим системы

С помощью [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) выполняется загрузка Uri с сохранением навигации.

## <a name="external"></a>Внешняя

С помощью стандартного параметра `OpenUrl` в главном приложении запускается браузер по умолчанию за пределами приложения.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Всегда будет запускаться браузер, установленный у пользователя по умолчанию, независимо от `BrowserLaunchMode`.

--------------

## <a name="api"></a>API

- [Исходный код класса Browser](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Документация по API Browser](xref:Xamarin.Essentials.Browser)
