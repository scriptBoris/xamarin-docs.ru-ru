---
title: Установка Xamarin в Visual Studio 2017
description: Этот документ описывает установку Xamarin в Visual Studio 2017. В нем рассмотрены требования, процесс установки и проверка установки.
ms.prod: xamarin
ms.assetid: E20D4463-368E-4B60-A059-F50DB8C5552D
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: d9f4717a4dd76e2fb3767c7baf9aaa868cfc21ff
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57199820"
---
# <a name="installing-xamarin-in-visual-studio-2017"></a>Установка Xamarin в Visual Studio 2017

<a name="requirements" />

Перед началом работы изучите [требования к системе](~/cross-platform/get-started/requirements.md).

## <a name="installation"></a>Установка

[!include[](~/cross-platform/includes/install-xamarin-windows.md)]

Чтобы проверить, установлена ли среда Xamarin, в Visual Studio 2017 щелкните меню **Справка**. Если Xamarin установлен, вы увидите пункт меню **Xamarin**, как показано на снимке экрана:

![Пункт Xamarin в меню "Справка"](windows-images/12-xamarin-menu-item.png "Xamarin menu item on the Help menu")

Кроме того, можно щелкнуть **Справка > О Microsoft Visual Studio** и, прокрутив список установленных продуктов, узнать, установлена ли среда Xamarin.

![Список установленных продуктов в Visual Studio 2015](windows-images/13-xamarin-is-installed.png "Visual Studio 2015 installed products screen")

Дополнительные сведения о поиске данных о версии см. в статье [Где можно найти сведения о версии и журналы?](~/cross-platform/troubleshooting/questions/version-logs.md)

## <a name="next-steps"></a>Следующие шаги

Установка Xamarin в Visual Studio 2017 позволяет приступить к написанию кода для приложений, но требует дополнительной настройки для создания и развертывания приложений в симуляторе, эмуляторе и на устройствах. Сведения в приведенных далее руководствах помогут завершить установку и начать создавать кроссплатформенные приложения.

### <a name="ios"></a>iOS

Дополнительные сведения см. в руководстве [Установка Xamarin.iOS в Windows](~/ios/get-started/installation/windows/index.md). 

1. [Установка Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/installation)
2. [Подключение Visual Studio к узлу сборки Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
3. [Настройка для разработки на базе iOS](~/ios/get-started/installation/device-provisioning/index.md) (требуется для запуска приложения на устройстве)
5. [Удаленный симулятор iOS](~/tools/ios-simulator/index.md)
6. [Введение в Xamarin.iOS для Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)

### <a name="android"></a>Android

Дополнительные сведения см. в руководстве [Установка Xamarin.Android в Windows](~/android/get-started/installation/windows.md).

1. [Конфигурация Xamarin.Android](~/android/get-started/installation/windows.md#configuration)
2. [Использование диспетчера пакетов SDK Android для Xamarin](~/android/get-started/installation/android-sdk.md?ide=vs)
3. [Эмулятор SDK для Android](~/android/get-started/installation/android-emulator/index.md)
4. [Настройка устройства для разработки](~/android/get-started/installation/set-up-device-for-development.md)
