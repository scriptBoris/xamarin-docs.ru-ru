---
title: Xamarin.Essentials карты
description: Класс карты в Xamarin.Essentials позволяет приложению откройте приложение в конкретное расположение или placemark установленных карт.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 445e2da84e9a9aaf1ce4d836af11cfba963b8cbb
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353958"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials: карты

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Сопоставляет** класс позволяет приложения, чтобы открыть приложение установленных карт в конкретное расположение или placemark.

## <a name="using-maps"></a>Использование карт

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Функциональность сопоставления работает путем вызова `OpenAsync` метод с `Location` или `Placemark` Открытие с необязательными `MapsLaunchOptions`.

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(location, options);
    }
}
```

При открытии с `Placemark` необходим следующие сведения:

* `CountryName`
* `AdminArea`
* `Thoroughfare`
* `Locality`

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var placemark = new Placemark
            {
                CountryName = "United States",
                AdminArea = "WA",
                Thoroughfare = "Microsoft Building 25",
                Locality = "Redmond"
            };
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>Методы расширения

Если у вас уже есть ссылка `Location` или `Placemark` можно использовать метод расширения встроенных `OpenMapsAsync` с необязательными `MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="platform-differences"></a>Различия платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `MapDirectionsMode` не поддерживается и не оказывает влияния.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `MapDirectionsMode` поддерживается задать режим направление по умолчанию при открытии приложения карт.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `MapDirectionsMode` не поддерживается и не оказывает влияния.

--------------

## <a name="platform-implementation-specifics"></a>Особенности реализации платформы

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android использует `geo:` схему Uri, чтобы запустить приложение карты на устройстве. Это может предложить пользователю выбрать уже существующего приложения, которая поддерживает эту схему Uri.  Xamarin.Essentials протестирована с Google карты, которая поддерживает эту схему.

# <a name="iostabios"></a>[iOS](#tab/ios)

Не сведения о конкретной реализации платформы.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Не сведения о конкретной реализации платформы.

--------------

## <a name="api"></a>API

- [Maps исходный код](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [Документация по службе карт API](xref:Xamarin.Essentials.Maps)
