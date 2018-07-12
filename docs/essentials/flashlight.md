---
title: 'Xamarin.Essentials: фонариком'
description: Этот документ описывает класс фонариком в Xamarin.Essentials, который имеет возможность включить или отключить камеру устройства флэш-памяти, чтобы превратить его в фонариком.
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a5c559653bff38c692f0b1d881d5d8f4cac3d383
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831415"
---
# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials: фонариком

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Фонариком** класс имеет возможность включить или отключить камеру устройства флэш-памяти, чтобы превратить его в фонариком.

## <a name="getting-started"></a>Начало работы

Чтобы получить доступ к **фонариком** функциональные возможности приведены следующие настройки платформы является обязательным.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Разрешения на фонариком и камеры являются обязательными и должны быть настроены в проект Android. Это можно сделать одним из следующих способов:

Откройте **AssemblyInfo.cs** файл **свойства** папку и добавьте:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

ИЛИ обновите манифест Android:

Откройте **AndroidManifest.xml** файл **свойства** папку и добавьте следующий код внутри класса **манифеста** узла.

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

Или щелкните правой кнопкой проект Anroid и откройте свойства проекта. В разделе **манифест Android** найти **необходимые разрешения:** области и проверьте **ФОНАРИКОМ** и **КАМЕРЫ** разрешения. Будет автоматически обновлена **AndroidManifest.xml** файла.

Добавив эти разрешения [Google Play будет автоматически отфильтровать устройства](http://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features) без конкретного оборудования. Можно обойти это, добавив следующее в файл AssemblyInfo.cs в проекте Android:

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

# <a name="iostabios"></a>[iOS](#tab/ios)

Дополнительная настройка не требуется.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Дополнительная настройка не требуется.

-----

## <a name="using-flashlight"></a>С помощью фонариком

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Фонариком можно включать и отключать через `TurnOnAsync` и `TurnOffAsync` методов:

```csharp
try
{
    // Turn On
    await Flashlight.TurnOnAsync();

    // Turn Off
    await Flashlight.TurnOffAsync();
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to turn on/off flashlight
}
```

## <a name="platform-implementation-specifics"></a>Особенности реализации платформы

### <a name="androidtabandroid-specifics"></a>[Android](#tab/android-specifics)

Класс фонариком был optmized, в зависимости от операционной системы устройства.

#### <a name="api-level-23-and-higher"></a>Уровень API 23 и более поздних версий

На более новые уровни API [Torch режим](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode) будет использоваться, чтобы включить или отключить вспышки устройства.

#### <a name="api-level-22-and-lower"></a>Уровень API 22 и ниже

Чтобы включить или отключить создается текстура поверхности камеры `FlashMode` единицы камеры. 

### <a name="iostabios-specifics"></a>[iOS](#tab/ios-specifics)

[AVCaptureDevice](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureDevice/) используется для включения и отключения Torch и флэш-режим устройства.

### <a name="uwptabuwp-specifics"></a>[UWP](#tab/uwp-specifics)

[LAMP](https://docs.microsoft.com/en-us/uwp/api/windows.devices.lights.lamp) используется для обнаружения первого lamp на задней панели устройства, чтобы включить или отключить.

-----

## <a name="api"></a>API

- [Фонариком исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Flashlight)
- [Документация по фонариком API](xref:Xamarin.Essentials.Flashlight)
