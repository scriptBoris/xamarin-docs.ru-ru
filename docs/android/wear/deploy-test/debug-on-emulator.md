---
title: Отладка Android Wear в эмуляторе
description: В этих статьях объясняется, как для отладки приложения Xamarin.Android износа в эмуляторе.
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 699fb3cc3a5730e8ab2c677feb7cdfbdcf106aeb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120558"
---
# <a name="debug-android-wear-on-an-emulator"></a>Отладка Android Wear в эмуляторе

_В этих статьях объясняется, как для отладки приложения Xamarin.Android износа в эмуляторе._

## <a name="debug-wear-on-emulator-overview"></a>Отладка Wear на обзор эмулятора

Разработки приложений Android Wear требуется запускать либо на физическом оборудовании, либо с помощью эмулятора или симулятора. Использование оборудования является лучшим, но не всегда самым целесообразным вариантом. Во многих случаях он может быть проще и рентабельнее смоделировать или сэмулировать оборудование Android Wear, с помощью эмулятора, как описано ниже. Если вы еще не знакомы с процессом развертывания и запуска приложения на Android Wear, см. в разделе [Wear Hello,](~/android/wear/get-started/hello-wear.md).

## <a name="configure-the-android-emulator"></a>Настройка эмулятора Android

Чтобы запустить приложение Wear на эмуляторе, необходимо установить Android SDK Android Emulator и его настройка для Android Wear. Android SDK Emulator установки и настройки информацию, см. в разделе [Настройка эмулятора Android](~/android/get-started/installation/android-emulator/index.md).

При создании виртуального устройства одежды, выберите профиль устройства Android Wear (такие как **Android Wear квадрат**). Для повышения производительности используйте износа **x86** ЦП/ABI, как показано в этом примере:

[![Пример конфигурации виртуального устройства Wear](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)


## <a name="launch-the-wear-virtual-device"></a>Запуск виртуального устройства Wear 

После создания виртуального устройства на Android Wear, вы можете его раскрывающееся меню "устройства" в интегрированной среде разработки до начала отладки. Если виртуальное устройство не доступны в раскрывающемся списке устройства, убедитесь, что проект Android *Wear* проект приложения (не проект приложения Android) и что его целевому уровню API установлен на том же API уровня, что и виртуальное устройство. Пример:

[![Выбор в меню Visual Studio устройства AVD, Wear](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

После запуска эмулятора Android Xamarin.Android развернет приложение Wear в эмуляторе. Эмулятор выполняет приложение с настроенным образом виртуального устройства.

Не удивляйтесь, если вы видите это (или другого экрана вставки) сначала. Контрольные значения эмулятор может занять некоторое время запуска: 

![Посмотрите, как эмулятор отображает минутку...](debug-on-emulator-images/please-wait.png)

Эмулятор можно оставить в рабочем режиме. Необязательно завершать его работу и перезапускать при каждом запуске приложения.

 
## <a name="summary"></a>Сводка
 
Это руководство описывает, как настроить эмулятор Android для разработки износа и запускать износа виртуальное устройство для отладки.
