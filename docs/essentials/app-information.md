---
title: Xamarin.Essentials. Сведения о приложении
description: В этом документе описывается класс AppInfo в Xamarin.Essentials, с помощью которого можно получить сведения о вашем приложении. Например, он предоставляет имя и версию приложения.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 00419fb746609464b49be343938905614c59ab29
ms.sourcegitcommit: 704d4cfd418c17b0e85a20c33a16d2419db0be71
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51691767"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials. Сведения о приложении

![Предварительная версия NuGet](~/media/shared/pre-release.png)

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
AppInfo.OpenSettings();
```

На этой странице параметров пользователь может изменять разрешения приложения и выполнять другие задачи для платформы.

## <a name="api"></a>API

- [Исходный код AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Документация по API AppInfo](xref:Xamarin.Essentials.AppInfo)
