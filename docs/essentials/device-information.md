---
title: 'Xamarin.Essentials: Сведения об устройстве'
description: Этот документ описывает класс DeviceInfo в Xamarin.Essentials, который предоставляет сведения об устройстве, что приложение выполняется на.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 18fe081372cc190e5ead2045f36d63652f8702c3
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353806"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Сведения об устройстве

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**DeviceInfo** класс предоставляет сведения об устройстве, приложение выполняется.

## <a name="using-deviceinfo"></a>С помощью DeviceInfo

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Через API предоставляются следующие сведения:

```csharp
// Device Model (SMG-950U, iPhone10,6)
var device = DeviceInfo.Model;

// Manufacturer (Samsung)
var manufacturer = DeviceInfo.Manufacturer;

// Device Name (Motz's iPhone)
var deviceName = DeviceInfo.Name;

// Operating System Version Number (7.0)
var version = DeviceInfo.VersionString;

// Platform (Android)
var platform = DeviceInfo.Platform;

// Idiom (Phone)
var idiom = DeviceInfo.Idiom;

// Device Type (Physical)
var deviceType = DeviceInfo.DeviceType;
```

## <a name="platformsxrefxamarinessentialsdeviceinfoplatforms"></a>[Платформы](xref:Xamarin.Essentials.DeviceInfo.Platforms)

`DeviceInfo.Platform` Устанавливает корреляцию между строковую константу, который сопоставляется операционной системы. Значения можно проверить с помощью `Platforms` класса:

- **DeviceInfo.Platforms.iOS** — iOS
- **DeviceInfo.Platforms.Android** — Android
- **DeviceInfo.Platforms.UWP** — универсальной платформы Windows
- **DeviceInfo.Platforms.Unsupported** — не поддерживается

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[Стили](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` Сопоставляет строковую константу, который сопоставляется с типом устройства, приложение выполняется на. Значения можно проверить с помощью `Idioms` класса:

- **DeviceInfo.Idioms.Phone** — телефон
- **DeviceInfo.Idioms.Tablet** — планшета
- **DeviceInfo.Idioms.Desktop** — рабочего стола
- **DeviceInfo.Idioms.TV** — ТВ
- **DeviceInfo.Idioms.Unsupported** — не поддерживается

## <a name="device-type"></a>Тип устройства

`DeviceInfo.DeviceType` Устанавливает корреляцию между перечисление для определения того, выполняется ли приложение на устройстве физический или виртуальный. Виртуальное устройство – симуляторе или эмуляторе.

## <a name="platform-implementation-specifics"></a>Особенности реализации платформы

# <a name="iostabios"></a>[iOS](#tab/ios)

iOS не предоставляет API для разработчиков получить имя для устройств iOS. Вместо этого такие как возвращается идентификатор оборудования _iPhone10 6_ ссылающийся на iPhone X. Сопоставление этих идентификаторов, не предоставляемых компанией Apple, но также можно найти на [iPhone вики-сайте](https://www.theiphonewiki.com/wiki/Models) (не Официальный источник источника).

--------------

## <a name="api"></a>API

- [Исходный код для отправки сведений об устройстве](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Документация по API для отправки сведений об устройстве](xref:Xamarin.Essentials.DeviceInfo)
