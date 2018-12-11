---
title: Xamarin.Essentials. Сведения о приложении
description: В этом документе описывается класс AppInfo в Xamarin.Essentials, с помощью которого можно получить сведения о вашем приложении. Например, он предоставляет имя и версию приложения.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 3e67b605e485b724ec11f2ac94dcf3d1aa77d5cf
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057303"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials. Сведения о приложении

Класс **AppInfo** предоставляет сведения о приложении.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-appinfo"></a>Использование AppInfo

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Получение сведений о приложении:

Через API предоставляются следующие сведения:

```csharp
// Application Name
var appName = AppInfo.Name;

// Package Name/Application Identifier (com.microsoft.testapp)
var packageName = AppInfo.PackageName;

// Application Version (1.0.0)
var version = AppInfo.VersionString;

// Application Build Number (1)
var build = AppInfo.BuildString;
```

## <a name="displaying-application-settings"></a>Отображение параметров приложения

Класс **AppInfo** также позволяет отобразить страницу параметров, поддерживаемых операционной системой для приложения:

```csharp
// Display settings page
AppInfo.ShowSettingsUI();
```

На этой странице параметров пользователь может изменять разрешения приложения и выполнять другие задачи для платформы.

## <a name="platform-implementation-specifics"></a>Особенности реализации для платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Сведения о приложении получены из следующих полей `AndroidManifest.xml`:

- **Build** — `android:versionCode` на узле `manifest`
- **Name** - `android:label` на узле `application`
- **PackageName**: `package` на узле `manifest`
- **VersionString** — `android:versionName` на узле `application`

# <a name="iostabios"></a>[iOS](#tab/ios)

Сведения о приложении получены из следующих полей `Info.plist`:

- **Build** — `CFBundleVersion`
- **Name** - `CFBundleDisplayName`, если задано. В противном случае `CFBundleName`
- **PackageName**: `CFBundleIdentifier`
- **VersionString** — `CFBundleShortVersionString`

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Сведения о приложении получены из следующих полей `Package.appxmanifest`:

- **Build** — использует `Build` из `Version` на узле `Identity`
- **Name** - `DisplayName` на узле `Properties`
- **PackageName**: `Name` на узле `Identity`
- **VersionString** –— `Version` на узле `Identity`


--------------

## <a name="api"></a>API

- [Исходный код AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Документация по API AppInfo](xref:Xamarin.Essentials.AppInfo)
