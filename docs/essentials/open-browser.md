---
title: 'Xamarin.Essentials: открытие браузера'
description: Класс Browser в Xamarin.Essentials позволяет приложению открывать веб-ссылку в предпочитаемом браузере оптимизированной системы или внешнем браузере.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 68bf7f51e9331c1296f26ad7083cf17ab8e480a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102287"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: браузер

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **Browser** позволяет приложению открыть веб-ссылку в предпочитаемом браузере оптимизированной системы или внешнем браузере.

## <a name="using-browser"></a>Использование класса Browser

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Чтобы использовать функции класса Browser, вызовите метод `OpenAsync` с параметрами `Uri` и `BrowserLaunchMode`.

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

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
