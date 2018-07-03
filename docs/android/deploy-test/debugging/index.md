---
title: Отладка Xamarin.Android на устройствах и эмуляторах
description: Тестирование и отладка приложения Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: 1ed6ec57365c5d3a861dd3fd947a2ad195ce5357
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935390"
---
# <a name="debugging"></a>Отладка

В этом разделе описываются принципы отладки приложения Xamarin.Android на устройствах или эмуляторах.

## <a name="debugging-overview"></a>Общие сведения об отладке

При разработке приложений Android приложения требуется запускать либо на физическом оборудовании, либо с помощью эмулятора. Использование оборудования является лучшим, но не всегда самым целесообразным вариантом. Во многих случаях может быть проще и рентабельнее смоделировать или сэмулировать оборудование Android с помощью одного из описанных ниже эмуляторов.

### <a name="debugging-on-the-android-emulatorandroiddeploy-testdebuggingdebug-on-emulatormd"></a>[Отладка приложений в Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)

В этой статье описано, как открыть Android Emulator из Visual Studio и запустить приложение на виртуальном устройстве.

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[Отладка на устройстве](~/android/deploy-test/debugging/debug-on-device.md)

В этой статье содержатся сведения о настройке физического устройства Android для развертывания на нем приложения Xamarin.Android непосредственно из Visual Studio или Visual Studio для Mac.

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Журнал отладки Android](~/android/deploy-test/debugging/android-debug-log.md)

Очень часто разработчики для отладки своих приложений используют `Console.WriteLine`. Однако на мобильной платформе, такой как Android, консоль отсутствует. На устройствах Android доступен журнал, который, скорее всего, потребуется вам при создании приложения. Иногда его называют **logcat** из-за команды, вводимой для его получения. Из этой статьи вы узнаете, как использовать **logcat**.

> [!WARNING]
> Обратите внимание, что **Xamarin Android Player** применять не рекомендуется. Дополнительные сведения см. в [объявлении в этой записи блога](https://blog.xamarin.com/live-from-dotnetconf-cycle-7-xamarin-studio-6-and-more/). Кроме того, начиная с Visual Studio 2017 **Visual Studio Android Emulator** использовать не рекомендуется.
