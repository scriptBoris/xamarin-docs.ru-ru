---
title: Общие вопросы и ответы
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 06aa6569301d1bfdbf9f6fd1e7397a38a9beb6f6
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350824"
---
# <a name="general-frequently-asked-questions"></a>Общие вопросы и ответы

## <a name="portable-class-libraries"></a>Переносимые библиотеки классов

### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[Как можно просмотреть, какие библиотеки поддерживаются в PCL](pcl-support-libraries.md)
В этом руководстве перечислены ресурсы и методы для определения, если существующие библиотеки поддерживается в различных целевых платформ переносимой библиотеки Классов, или могут быть преобразованы в профиль PCL.

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[API отражения PCL](pcl-reflection.md)
Корпорация Майкрософт разработала новый интерфейс API отражения для использования в переносимые библиотеки классов. Если у вас есть некоторые существующего кода отражения, который вы хотите переместить в переносимую библиотеку Классов, он может не работать.

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[Практический пример PCL. Как устранять проблемы, связанные с System.Diagnostics.Tracing для пакета NuGet потока данных библиотеки параллельных задач Майкрософт](pcl-case-study.md)
Xamarin.iOS и Xamarin.Android не следует реализовывать каждый профиль PCL, они позволяют как ссылки на 100%. Для практического удобства в Visual Studio для Mac, Visual Studio и диспетчер пакетов NuGet проекты Xamarin разрешает использовать несколько профилей, которые имеют только неполные реализаций. Например, ни Xamarin.iOS и Xamarin.Android в настоящее время включает в себя полную реализацию типы в `System.Diagnostics.Tracing` переносимой библиотеки Классов пространства имен. Это можно обойти, переключившись в проект приложения для ссылки на portable-net45 + win8 + wp8 + wpa81 версию библиотеки потоков данных TPL.

## <a name="nuget-packages--xamarin-components"></a>Пакеты NuGet и компоненты Xamarin
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[Как обновить NuGet](nuget-update.md)
Обновления NuGet, расширений и надстроек можно найти в разделе **обновления** вкладке **диспетчер пакетов NuGet**. Подробные навигации для поиска обновлений в Visual Studio для Mac и Visual Studio находится в этом руководстве.

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[Как перейти на использование более ранней версии пакета NuGet](nuget-package-downgrade.md)
Visual Studio для Mac, так и с помощью Visual Studio имеют функции для выбор более старых версиях пакетов и установка их автоматически. как и как работает обновление пакетов.

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[Ошибка отсутствующих пакетов после обновления пакетов NuGet](nuget-packages-missing.md)
Эта проблема обнаружена главным образом в Xamarin.Forms примеры приложений решений, но вероятность эта проблема может произойти в любой проект, который использует пакеты NuGet.

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[Объединение компонентов Сервисов Google Play и NuGet](gps-components-nuget.md)
Используется, существует несколько компонентов служб Google-Play и пакеты NuGet, но чтобы упростить этот процесс для разработчиков, мы теперь единой наших компонентов и NuGet пакеты на две части. В практически в каждом случае следует использовать службы Google Play. Единственная причина для использования пакета (Froyo) — Если вы активно используете Froyo.

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[Где на компьютере хранятся компоненты](component-storage.md)
При установке компонента Xamarin в проекте приложения, он помещается в двух местах, приведенный в данном руководстве.


## <a name="troubleshooting"></a>Устранение неполадок
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[Где я могу найти информацию о версии и журналы](version-logs.md)
В этом руководстве подробно, где можно найти большинство диагностических сведений, который может использоваться для устранения неполадок Xamarin.

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[Когда и как следует указывать отчет об ошибке](howto-file-bug.md)
В этом руководстве советы для заполнения отчетов об ошибках высокого качества, чтобы обеспечить возможность определить причину (и любые возможные исправления) из-за более эффективно наших инженеров.

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[Почему Xamarin не поддерживает Jenkins](xamarin-jenkins.md)
Jenkins — это набор CI открытым исходным кодом; из-за количества проблем, вызванных непосредственно Jenkins *сам* нужно будет отправить как проблемы, с которой был взят код, например [основного репозитория Jenkins](https://github.com/jenkinsci/jenkins), или из репозитория для [ Jenkins.App](https://github.com/stisti/jenkins-app).

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[Какие параметры проекта нужны отладчику](debugger-settings.md)
Чтобы отладчик будет работать неправильно (попаданий точки останова, журналы отладки отображения т. д.) отображаемая информация разработчика инструментирования и отладки должны быть включены. В этом руководстве подробно описано, как найти и активировать эти параметры.

