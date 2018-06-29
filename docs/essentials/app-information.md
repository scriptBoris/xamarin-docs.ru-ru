---
title: 'Xamarin.Essentials: Сведения о приложении'
description: Этот документ описывает класс AppInfo в Xamarin.Essentials, который предоставляет сведения о приложении. Например он предоставляет имя приложения и версии.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7e79b3003f41b8de22950624e44e8c9e0e7e7e31
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080278"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Сведения о приложении

![Предварительная версия NuGet](~/media/shared/pre-release.png)

**AppInfo** класс предоставляет сведения о приложении.

## <a name="using-appinfo"></a>С помощью AppInfo

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Получение сведений о приложении:

Через API-Интерфейс предоставляются следующие сведения:

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

**AppInfo** класс также может отображать страницу параметров, поддерживаемых операционной системы для приложения:

```csharp
// Display settings page
AppInfo.OpenSettings();
```

Эта страница параметров позволяет пользователю изменить разрешения для приложений и выполнения других задач конкретную платформу.

## <a name="api"></a>API

- [AppInfo исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Документация по AppInfo API](xref:Xamarin.Essentials.AppInfo)
