---
title: Xamarin.Essentials. Сведения об устройстве
description: В этом документе описывается класс DeviceInfo в Xamarin.Essentials, с помощью которого можно получить сведения об устройстве, в котором выполняется приложение.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: b78c04d30871552f9b1e18a42c871e24464c4802
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898957"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials. Сведения об устройстве

Класс **DeviceInfo** предоставляет сведения об устройстве, в котором выполняется приложение.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-deviceinfo"></a>Использование класса DeviceInfo

Добавьте в свой класс ссылку на Xamarin.Essentials:

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

`DeviceInfo.Platform` устанавливает корреляцию с постоянной строкой, которая сопоставляется с операционной системой. Значения можно проверить с помощью структуры `DevicePlatform`:

- **DevicePlatform.iOS** — iOS
- **DevicePlatform.Android** — Android
- **DevicePlatform.UWP** — UWP
- **DevicePlatform.Unknown** — неизвестно

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[Idioms](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` коррелирует постоянную строку, которая сопоставляется с типом устройства, на котором выполняется приложение. Значения можно проверить с помощью структуры `DeviceIdiom`:

- **DeviceIdiom.Phone** — телефон
- **DeviceIdiom.Tablet** — планшет
- **DeviceIdiom.Desktop** — компьютер
- **DeviceIdiom.TV** — телевизор
- **DeviceIdiom.Watch** — часы
- **DeviceIdiom.Unknown** — неизвестно

## <a name="device-type"></a>Тип устройства

Тип `DeviceInfo.DeviceType` коррелирует перечисление, чтобы определить тип устройства, на котором выполняется приложение (физическое или виртуальное). Виртуальное устройство является симулятором или эмулятором.

## <a name="platform-implementation-specifics"></a>Особенности реализации для платформ

# <a name="iostabios"></a>[iOS](#tab/ios)

В iOS не предоставляется API для разработчиков, чтобы получить имя конкретного устройства iOS. Вместо этого возвращается идентификатор аппаратного обеспечения, например _iPhone10,6_, который относится к iPhone X. Сопоставление этих идентификаторов не обеспечивается в Apple, но их можно найти в [Википедии iPhone (The iPhone Wiki)](https://www.theiphonewiki.com/wiki/Models) (неофициальный источник).

--------------

## <a name="api"></a>API

- [Исходный код DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Документация по API DeviceInfo](xref:Xamarin.Essentials.DeviceInfo).
