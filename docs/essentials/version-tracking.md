---
title: 'Xamarin.Essentials: Отслеживание версий'
description: Класс VersionTracking в Xamarin.Essentials позволяет проверить версию приложения и номера сборок, а также просматривать дополнительные сведения о таких, если это первый раз когда-нибудь запущенное приложение или текущую версию, получить предыдущей сборки сведения и многое другое.
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2c092d6767045f0af956c5dab74801077dadb51f
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815652"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials: Отслеживание версий

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**VersionTracking** класс позволяет проверить версию приложения и номера сборок, а также просматривать дополнительные сведения о таких, если это первый раз когда-нибудь запущенное приложение или текущую версию, получить предыдущего сведения о сборке и многое другое.

## <a name="using-version-tracking"></a>Использование отслеживания версии

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

При первом использовании **VersionTracking** класс, он будет запущен, отслеживание текущей версии. Необходимо вызвать `Track` раннее только в приложении каждый раз, он загружается отслеживаемых сведений о текущей версии:

```csharp
VersionTracking.Track();
```

После первоначального `Track` вызывается считываются сведения о версии:

```csharp

// First time ever launched application
var firstLaunch = VersionTracking.IsFirstLaunchEver;

// First time launching current version
var firstLaunchCurrent = VersionTracking.IsFirstLaunchForCurrentVersion;

// First time launching current build
var firstLaunchBuild = VersionTracking.IsFirstLaunchForCurrentBuild;

// Current app version (2.0.0)
var currentVersion = VersionTracking.CurrentVersion;

// Current build (2)
var currentBuild = VersionTracking.CurrentBuild;

// Previous app version (1.0.0)
var previousVersion = VersionTracking.PreviousVersion;

// Previous app build (1)
var previousBuild = VersionTracking.PreviousBuild;

// First version of app installed (1.0.0)
var firstVersion = VersionTracking.FirstInstalledVersion;

// First build of app installed (1)
var firstBuild = VersionTracking.FirstInstalledBuild;

// List of versions installed (1.0.0, 2.0.0)
var versionHistory = VersionTracking.VersionHistory;

// List of builds installed (1, 2)
var buildHistory = VersionTracking.BuildHistory;
```

## <a name="platform-implementation-specifics"></a>Особенности реализации платформы

Все сведения о версии хранится с использованием [предпочтения](preferences.md) API в Xamarin.Essentials и хранится вместе с имени файла **.xamarinessentials [YOUR-APP-пакет-ID]**.

При удалении приложения приведет к _LocalSettings_и все версии, данные для удаления отслеживания.

## <a name="api"></a>API

- [Исходный код для отслеживания версии](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/VersionTracking)
- [Документация по API отслеживания версии](xref:Xamarin.Essentials.VersionTracking)
