---
title: 'Xamarin.Essentials: средство запуска'
description: Класс Launcher в Xamarin.Essentials позволяет приложению открыть URI средствами системы.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 8f5ef8ef97999e9e85944d9fa9d4e57660779a48
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115787"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: средство запуска

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **Launcher** позволяет приложению открыть URI средствами системы. Это часто используется при глубокой привязке к пользовательским схемам URI другого приложения. Если нужно открыть в браузере веб-сайт, следует обратиться к API **[Browser](open-browser.md)**.

## <a name="using-launcher"></a>Использование класса Launcher

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Чтобы использовать функции класса Launcher, вызовите метод `OpenAsync` и передайте параметр `string` или `Uri` для открытия. При необходимости с помощью метода `CanOpenAsync` можно проверить, может ли приложение на устройстве обработать схему URI.

```csharp
public class LauncherTest
{
    public async Task OpenRideShareAsync()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

## <a name="platform-differences"></a>Различия платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Задача, возвращенная методом `CanOpenAsync`, выполняется немедленно.

# <a name="iostabios"></a>[iOS](#tab/ios)

Если конечное приложение на этом устройстве никогда ранее не открывалось с помощью метода `OpenAsync` из вашего приложения, iOS однократно предложит пользователю разрешить открывать его с помощью вашего приложения.

Задача, возвращенная методом `CanOpenAsync`, выполняется немедленно.

Дополнительные сведения о реализации iOS доступны [здесь](https://developer.xamarin.com/api/member/UIKit.UIApplication.CanOpenUrl/p/Foundation.NSUrl/).

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Различия платформ отсутствуют.

-----

## <a name="api"></a>API

- [Исходный код класса Launcher](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Документация по API Launcher](xref:Xamarin.Essentials.Launcher)
