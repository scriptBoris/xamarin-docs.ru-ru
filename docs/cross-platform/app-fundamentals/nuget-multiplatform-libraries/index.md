---
title: Проекты библиотеки многоплатформенных NuGet (также называемые Nugetizer 3000)
description: В этом документе описывается, как использовать средство Nugetizer 3000 для автоматического создания пакетов NuGet для совместного использования кода между платформами.
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 1d48bc28aa4477361ca8057fda91ee3258f36a73
ms.sourcegitcommit: 46bb04016d3c35d91ff434b38474e0cb8197961b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39270432"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>Проекты многоплатформенная библиотека NuGet (Nugetizer 3000)

_Автоматически создайте пакеты NuGet для совместного использования кода между платформами с использованием «Nugetizer 3000"!_

Можно автоматически создавать пакеты NuGet для совместного использования кода между платформами с использованием _Nugetizer 3000_. В результате можно создать пакеты NuGet из имеющиеся проекты библиотеки или создав новый **проект библиотеки многоплатформенных**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Nugetizer 3000 входит в состав Visual Studio для Mac &ndash; искать **библиотеки > Библиотека Mulitplatform** проекта в **файл > создать** окна:

[![](images/mulitplatform-library-sml.png "Создать новое окно многоплатформенные библиотеки")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Чтобы использовать Nugetizer 3000 в Visual Studio, воспользуйтесь ссылкой [скачайте и запустите установщик VSIX](http://bit.ly/nugetizer-2017).

-----

## <a name="building-nuget-packages"></a>Построение пакетов NuGet

После настройки каждой сборки проекта выводит полный пакет NuGet, который используется внутренне совместное использование кода с другими приложениями или переданные [NuGet.org](https://www.nuget.org).

Существует три варианта использования этой функции:

- [Имеющиеся проекты библиотеки](existing-library.md)

  Создание пакета NuGet из существующих проектов переносимой библиотеки Классов (или .NET Standard).

- [Создание нового проекта многоплатформенная библиотека](single-codebase.md)

  Создайте новую библиотеку для совместного использования общего кода с помощью NuGet, с помощью переносимой библиотеки Классов или .NET Standard.

- [Создание новых проектов библиотеки платформы](platform-specific.md)

  Создайте новую библиотеку и NuGet, которая включает в себя специфические для платформы код для iOS и Android, а также использует общий проект для размещения общего кода и проекты под конкретные платформы для поддержки функциональных возможностей конкретного iOS или Android.

Ссылаться на [руководство по метаданным](metadata.md) сведения об обязательных и необязательных метаданных, которая должна быть добавлена для любого пакета NuGet.

## <a name="further-nuget-information"></a>Дополнительные сведения о NuGet

Дополнительные сведения о [ручного создания пакеты NuGet для Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md) и [включить пакет NuGet в приложении](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Корпорации Майкрософт [документации по NuGet](https://docs.microsoft.com/nuget/) содержат более подробную информацию на **.nupkg** формат и использование пакетов NuGet в Visual Studio.

Обсуждение разработки для проектов пакет NuGet (так называемые) NuGetizer 3000) можно найти в [репозитории NuGet GitHub](https://github.com/NuGet/Home/wiki/NuGetizer-3000).

## <a name="related-links"></a>Связанные ссылки

- [Варианты использования NuGetizer 3000](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [Вручную создайте пакеты NuGet для Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)
- [Документация по NuGet](https://docs.microsoft.com/nuget/)
- [Заметки о выпуске](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#NuGetizer_3000)
