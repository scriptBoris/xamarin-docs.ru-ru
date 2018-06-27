---
title: Настройка Google Android Emulator
description: Для имитации разнообразных устройств эмулятор Google Android Emulator можно запускать в различных конфигурациях. Это руководство описывает, как подготовить Android Emulator для тестирования приложения.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: e5ba2cc23ea9751ca60644d3eb5b7e3f31bbb6bb
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732535"
---
# <a name="google-android-emulator-setup"></a>Настройка Google Android Emulator

_Это руководство описывает, как подготовить Google Android Emulator для тестирования приложения._


## <a name="overview"></a>Обзор

Для имитации разнообразных устройств эмулятор Google Android Emulator можно запускать в различных конфигурациях. Каждая конфигурация называется _виртуальным устройством_. При развертывании и тестировании приложения в эмуляторе вы выбираете предварительно настроенное или пользовательское виртуальное устройство, имитирующее физическое устройство Android, такое как телефон Pixel или Nexus.

Указанные ниже разделы описывают, как ускорить Google Android Emulator для достижения максимальной производительности, как использовать Android Device Manager для создания и настройки виртуальных устройств и как настроить свойства профиля для виртуального устройства. Кроме того, в разделе об устранении неполадок описаны общие проблемы настройки и приведены варианты их устранения.

## <a name="sections"></a>Разделы

### <a name="hardware-acceleration-for-emulator-performanceandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[Аппаратное ускорение для производительной работы эмулятора](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Сведения о подготовке компьютера для достижения максимальной производительности Android Emulator.
Так как без аппаратного ускорения Google Android Emulator может работать слишком медленно, перед его использованием на компьютере рекомендуется включить аппаратное ускорение.

### <a name="managing-virtual-devices-with-the-android-device-managerandroidget-startedinstallationandroid-emulatordevice-managermd"></a>[Управление виртуальными устройствами с помощью Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)

Сведения об использовании Android Device Manager для создания и настройки виртуальных устройств.

### <a name="editing-android-virtual-device-propertiesandroidget-startedinstallationandroid-emulatordevice-propertiesmd"></a>[Изменение свойств виртуальных устройств Android](~/android/get-started/installation/android-emulator/device-properties.md)

Сведения об изменении свойства профиля виртуального устройства Android с помощью Android Device Manager.

### <a name="troubleshooting-emulator-setup-problemsandroidget-startedinstallationandroid-emulatortroubleshootingmd"></a>[Устранение неполадок при настройке эмулятора](~/android/get-started/installation/android-emulator/troubleshooting.md)

Сведения о диагностике и устранении проблем с Android Device Manager, которые могут возникать при настройке Android Emulator.


После настройки Android Emulator см. статью [Отладка с помощью Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md), чтобы узнать, как запустить эмулятор и использовать его для тестирования и отладки приложения.


> [!NOTE]
> В Android SDK Tools **26.0.1** и более поздних версий компания Google исключила поддержку существующих диспетчеров AVD и SDK, заменив их новыми средствами интерфейса командной строки (CLI). В связи с этим изменением теперь вместо диспетчеров устройств или пакетов SDK Google для Android Tools 26.0.1 и более поздних версий используются диспетчеры устройств или пакетов SDK Xamarin. Дополнительные сведения о диспетчере пакетов SDK Xamarin см. в разделе [Настройка пакета SDK для Android](~/android/get-started/installation/android-sdk.md).

