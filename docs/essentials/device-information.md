---
title: Xamarin.Essentials. Сведения об устройстве
description: В этом документе описывается класс DeviceInfo в Xamarin.Essentials, с помощью которого можно получить сведения об устройстве, в котором выполняется приложение.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 530b04446703d78452357b2c9f9089e59ebf6e6c
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674817"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials. Сведения об устройстве

![Предварительная версия NuGet](~/media/shared/pre-release.png)

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

`DeviceInfo.Platform` устанавливает корреляцию с постоянной строкой, которая сопоставляется с операционной системой. Значения можно проверить с помощью класса `Platforms`:

- **DeviceInfo.Platforms.iOS** — iOS
- **DeviceInfo.Platforms.Android** — Android
- **DeviceInfo.Platforms.UWP** — UWP
- **DeviceInfo.Platforms.Unsupported** — не поддерживается

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[Idioms](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` коррелирует постоянную строку, которая сопоставляется с типом устройства, на котором выполняется приложение. Значения можно проверить с помощью класса `Idioms`:

- **DeviceInfo.Idioms.Phone** — телефон
- **DeviceInfo.Idioms.Tablet** — планшет
- **DeviceInfo.Idioms.Desktop** — настольный компьютер
- **DeviceInfo.Idioms.TV** — ТВ
- **DeviceInfo.Idioms.Unsupported** — не поддерживается

## <a name="device-type"></a>Тип устройства

Тип `DeviceInfo.DeviceType` коррелирует перечисление, чтобы определить тип устройства, на котором выполняется приложение (физическое или виртуальное). Виртуальное устройство является симулятором или эмулятором.

## <a name="platform-implementation-specifics"></a>Особенности реализации для платформ

# <a name="iostabios"></a>[iOS](#tab/ios)

В iOS не предоставляется API для разработчиков, чтобы получить имя конкретного устройства iOS. Вместо этого возвращается идентификатор аппаратного обеспечения, например _iPhone10,6_, который относится к iPhone X. Сопоставление этих идентификаторов не обеспечивается в Apple, но их можно найти в [Википедии iPhone (The iPhone Wiki)](https://www.theiphonewiki.com/wiki/Models) (неофициальный источник).

--------------

## <a name="api"></a>API

- [Исходный код DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Документация по API DeviceInfo](xref:Xamarin.Essentials.DeviceInfo).
