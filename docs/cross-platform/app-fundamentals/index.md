---
title: Совместное использование кода на нескольких платформах
description: Этот документ содержит ссылки на различные руководства, описывающие способы совместного использования кода, включая переносимые библиотеки классов, общие проекты, .NET Standard и NuGet.
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: bfca620848bef174e78d9d34b6fdc497dda8f1de
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233228"
---
# <a name="sharing-code-on-multiple-platforms"></a>Совместное использование кода на нескольких платформах

В этой статье рассматривается различные параметры, доступные для совместного использования кода на платформах, включая Windows, Android, iOS и многое другое.

## <a name="code-sharing-overviewcode-sharingmd"></a>[Совместное использование Обзор кода](code-sharing.md)

Дополнительные сведения о различных параметры, доступные для проектов Xamarin, включая стандартные библиотеки .NET и проектах общих совместного использования кода. Переносимые библиотеки классов, также поддерживаются, однако считается устаревшим в связи с .NET Standard.

## <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET standard является предпочтительным при совместное использование кода несколькими платформами. Код создан для конкретной версии (2.0 обеспечивает лучший API совместимости с существующим кодом .NET Framework) и может быть принято элементом других проектов, которые поддерживают этот уровень или более поздней версии. Проектов .NET standard, поддерживаются в Visual Studio 2017 и Visual Studio для Mac.

## <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[Общие проекты](~/cross-platform/app-fundamentals/shared-projects.md)

Общие проекты позволяют писать общий код, на который ссылается несколько проектов различных приложений. Код компилируется как часть каждого ссылающийся проект и могут включать директивы компилятора для внедрения функциональных возможностей платформы базы общего кода. В этой статье рассматриваются как общие проекты работают и как создать и использовать их с проектами Xamarin.

## <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[Переносимые библиотеки классов](~/cross-platform/app-fundamentals/pcl.md)

Проекты переносимой библиотеки классов можно создавать и распространять сборки, содержащие общий код для запуска на нескольких платформах. Для создания переносимой библиотеки классов (или «PCL») сначала выбрать какие платформы для целевой, а затем написать код для подмножество .NET Framework, которая доступна в профиле, определенных для этих платформ. PCL считается устаревшим в последних версиях Visual Studio; Разработчики, рекомендуется вместо этого использовать .NET Standard 2.0.

## <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[Проекты NuGet: Обеспечивает реализацию многоплатформенных библиотек для совместного использования кода](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

Пакеты NuGet можно автоматически создавать из проектов переносимой библиотеки Классов или .NET standard; и проектах общих можно упаковать в пакеты NuGet «с подменой», с помощью отдельный тип проекта NuGet. В этом разделе описывается создание пакетов NuGet для каждого сценария совместного использования кода.

## <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[Вручную созданию пакетов NuGet для Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)

Советы по созданию пакетов NuGet, которые работают на платформе Xamarin.

## <a name="use-cc-libraries-in-cross-platform-xamarin-projectscross-platformcppindexmd"></a>[Использование библиотек C/C++ в проектах Xamarin кросс платформенные](~/cross-platform/cpp/index.md)

Этот метод позволяет отделить развития библиотек C/C++, C# привязки в приложения Xamarin и NuGet. Функциональность обеспечивается библиотеки платформы машинный код C/C++, но изолированный от окончательного приложения Xamarin, позволяя производительности максимальное возможное с без дублирования кода весь код конкретной платформы. 
