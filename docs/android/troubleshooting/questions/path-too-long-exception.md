---
title: Как устранить ошибку PathTooLongException?
description: В этой статье объясняется, как разрешить исключение PathTooLongException, которая может возникнуть при построении приложения.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 4cb3e13ebbe3d9e8aed153528a35ab16c92e2145
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108708"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Как устранить ошибку PathTooLongException?

## <a name="cause"></a>Причина

Имена путей, созданный в проект Xamarin.Android может быть довольно длинными.
Например во время сборки могут создаваться путь следующего вида:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

В Windows (где Максимальная длина пути составляет [260 символов](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), **PathTooLongException** может создаваться во время построения проекта, если созданного пути превышает максимальную длину. 

## <a name="fix"></a>Исправление

Начиная с Xamarin.Android 8.0, `UseShortFileNames` MSBuild может быть установлено для обхода этой ошибки. Если присвоить этому свойству `True` (по умолчанию используется `False`), процесс сборки использует более короткие имена пути, чтобы уменьшить вероятность создания **PathTooLongException**.
Например, если `UseShortFileNames` присваивается `True`, указанном выше пути сокращено до пути, аналогичную следующей:

**C:\\некоторые\\Directory\\решение\\проекта\\obj\\Отладка\\lp\\1\\jl\\активы**

Чтобы задать это свойство, добавьте следующее свойство MSBuild для проекта **.csproj** файла:

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Если установлен этот флаг не исправляет **PathTooLongException** ошибки, другой подход: определить [распространенный корень промежуточные выходные](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) для проектов в решении, задав `IntermediateOutputPath` в проект **.csproj** файл. Попробуйте использовать относительно короткий путь. Пример:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Дополнительные сведения о настройке свойств сборки см. в разделе [процесса сборки](~/android/deploy-test/building-apps/build-process.md).
