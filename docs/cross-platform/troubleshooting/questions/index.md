---
title: Общие вопросы и ответы
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: asb3993
ms.author: amburns
ms.openlocfilehash: b81f5c09ea06acf87449448f43b6c0474a6737b0
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33917477"
---
# <a name="general-frequently-asked-questions"></a>Общие вопросы и ответы

## <a name="portable-class-libraries"></a>Переносимые библиотеки классов
### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[Как можно просмотреть, какие библиотеки поддерживаются в PCL](pcl-support-libraries.md)
В этом руководстве перечислены ресурсы и методы для определения, если существующие библиотеки поддерживается на различных целевых платформах PCL, или могут быть преобразованы к профилю PCL.

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[API отражения PCL](pcl-reflection.md)
Корпорация Майкрософт разработала новый API отражения для использования в переносимой библиотеки классов. Если у вас есть код отражения, которую требуется переместить в PCL, он может не работать.

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[Практический пример PCL. Как устранять проблемы, связанные с System.Diagnostics.Tracing для пакета NuGet потока данных библиотеки параллельных задач Майкрософт](pcl-case-study.md)
Xamarin.iOS и Xamarin.Android, не следует реализовывать каждый профиль PCL, они позволяют как ссылки на 100%. Для удобства практической работы в Visual Studio для Mac, Visual Studio и диспетчер пакетов NuGet проектов Xamarin разрешить несколько профилей, которые имеют только неполные реализации. Например, не Xamarin.iOS и Xamarin.Android в настоящее время содержит полную реализацию типов в `System.Diagnostics.Tracing` PCL пространства имен. Это можно обойти путем переключения проект приложения для ссылки portable net45 + win8 + wp8 + wpa81 версию библиотеки потоков данных TPL.

## <a name="nuget-packages--xamarin-components"></a>Пакеты NuGet & компоненты Xamarin
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[Как обновить NuGet](nuget-update.md)
NuGet обновления, расширения и надстроек можно найти в разделе **обновления** вкладке **диспетчера пакетов NuGet**. В данном руководстве — подробные навигации для поиска обновлений в Visual Studio для компьютеров Mac и Visual Studio.

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[Как перейти на использование более ранней версии пакета NuGet](nuget-package-downgrade.md)
Visual Studio для Mac и Visual Studio имеется для выбора более старых версий пакетов и установка их автоматически. как и как работает обновление пакетов.

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[Ошибка отсутствующих пакетов после обновления пакетов NuGet](nuget-packages-missing.md)
Эта проблема обнаружена в основном в Xamarin.Forms примеров приложений решений, но вероятность эта проблема может произойти на любой проект, который использует пакеты NuGet.

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[Объединение компонентов Сервисов Google Play и NuGet](gps-components-nuget.md)
Используется, существует несколько компонентов служб воспроизвести Google и пакеты NuGet, но чтобы упростить для разработчиков, мы теперь одинаково наши компоненты и NuGet пакеты на две. В этом следует использовать службы Google Play. Единственная причина для использования пакета (Froyo) — Если вы активно используете Froyo.

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[Где на компьютере хранятся компоненты](component-storage.md)
При установке компонента Xamarin в проекте приложения, он помещается в двух местах, приведенный в данном руководстве.


## <a name="troubleshooting"></a>Устранение неполадок
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[Где я могу найти информацию о версии и журналы](version-logs.md)
В этом руководстве подробно описываются где найти большинство диагностической информации, которая может использоваться для устранения неполадок Xamarin.

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[Когда и как следует указывать отчет об ошибке](howto-file-bug.md)
В этом руководстве содержатся советы по отчеты об ошибках высокого качества, хранение, что наши инженеры для определения причины (и все возможные исправления) проблемы более эффективно.

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[Почему Xamarin не поддерживает Jenkins](xamarin-jenkins.md)
Jenkins — это набор элементов конфигурации открытым исходным кодом; из-за указанного количества проблем, вызванных непосредственно Jenkins *сам* будет необходимо будет считаться проблем, с которой был взят код; например, [основного репозитория Jenkins](https://github.com/jenkinsci/jenkins), или репозиторий для [ Jenkins.App](https://github.com/stisti/jenkins-app).

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[Какие параметры проекта нужны отладчику](debugger-settings.md)
Чтобы отладчик мог работать должным образом (попаданий точки останова, журналы отладки отображения, т. д.) отображение информации о разработчика инструментирования и отладки должны быть включены. В этом руководстве описывается поиск и активировать эти параметры.

