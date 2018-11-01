---
title: Настройка эмулятора Android
description: Для имитации разнообразных устройств Android Emulator можно запускать в различных конфигурациях. Это руководство описывает, как подготовить Android Emulator для тестирования приложения.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/27/2018
ms.openlocfilehash: 6ce8f633cdc0fd4616673eb047d640a8703b3a30
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102533"
---
# <a name="android-emulator-setup"></a>Настройка эмулятора Android

_Это руководство описывает, как подготовить Android Emulator для тестирования приложения._


## <a name="overview"></a>Обзор

Для имитации разнообразных устройств Android Emulator можно запускать в различных конфигурациях. Каждая конфигурация называется _виртуальным устройством_. При развертывании и тестировании приложения в эмуляторе вы выбираете предварительно настроенное или пользовательское виртуальное устройство, имитирующее физическое устройство Android, такое как телефон Pixel или Nexus.

В разделах ниже описано, как ускорить Android Emulator для достижения максимальной производительности, как использовать Android Device Manager для создания и настройки виртуальных устройств и как настроить свойства профиля для виртуального устройства. Кроме того, в разделе об устранении неполадок описаны общие проблемы эмулятора и приведены варианты их устранения.

## <a name="sections"></a>Разделы

### <a name="hardware-acceleration-for-emulator-performanceandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[Аппаратное ускорение для производительной работы эмулятора](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Как подготовить свой компьютер для достижения максимальной производительности Android Emulator с помощью технологии виртуализации Hyper-V или HAXM. Так как без аппаратного ускорения Android Emulator может работать слишком медленно, перед его использованием на компьютере рекомендуется включить аппаратное ускорение.

### <a name="managing-virtual-devices-with-the-android-device-managerandroidget-startedinstallationandroid-emulatordevice-managermd"></a>[Управление виртуальными устройствами с помощью Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)

Сведения об использовании Android Device Manager для создания и настройки виртуальных устройств.

### <a name="editing-android-virtual-device-propertiesandroidget-startedinstallationandroid-emulatordevice-propertiesmd"></a>[Изменение свойств виртуальных устройств Android](~/android/get-started/installation/android-emulator/device-properties.md)

Сведения об изменении свойств профиля виртуального устройства с помощью Android Device Manager.

### <a name="android-emulator-troubleshootingandroidget-startedinstallationandroid-emulatortroubleshootingmd"></a>[Устранение неполадок в Android Emulator](~/android/get-started/installation/android-emulator/troubleshooting.md)

В этой статье описаны наиболее типичные ошибки и предупреждения, возникающие в Android Emulator, а также способы их устранения.

После настройки Android Emulator см. статью [Отладка в Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md), чтобы узнать, как запустить эмулятор и использовать его для тестирования и отладки приложения.


> [!NOTE]
> В Android SDK Tools **26.0.1** и более поздних версий компания Google исключила поддержку существующих диспетчеров AVD и SDK, заменив их новыми средствами интерфейса командной строки (CLI). В связи с этим изменением теперь вместо диспетчеров устройств или пакетов SDK Google для Android Tools 26.0.1 и более поздних версий используются диспетчеры устройств или пакетов SDK Xamarin. Дополнительные сведения о диспетчере пакетов SDK Xamarin см. в разделе [Настройка пакета SDK для Android под Xamarin.Android](~/android/get-started/installation/android-sdk.md).

