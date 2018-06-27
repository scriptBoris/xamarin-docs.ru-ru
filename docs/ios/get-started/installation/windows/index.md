---
title: Установка Xamarin.iOS в Windows
description: Этот документ описывает настройку компьютера Windows и узла сборки Mac, а также сопряжение Windows с Mac для разработки Xamarin.iOS.
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/16/2018
ms.openlocfilehash: 2bff37aba9b961b7308bf261377951dc96bd8e34
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786068"
---
# <a name="installing-xamarinios-on-windows"></a>Установка Xamarin.iOS в Windows

_В этой статье описывается настройка компьютера Windows и узла сборки Mac для разработки Xamarin.iOS._

## <a name="overview"></a>Обзор

Для сборки приложений Xamarin.iOS с помощью Visual Studio 2017 в Windows вам потребуются следующие компоненты.
 
-  Компьютер Windows с установленной Visual Studio 2017. Это может быть физический компьютер или виртуальная машина.
    - [Требования к системе Windows](~/cross-platform/get-started/requirements.md#windows-requirements)
    
-  Доступный по сети компьютер Mac, на котором настроены средства сборки Apple и Xamarin.iOS. Visual Studio 2017 обращается к этому компьютеру по сети, чтобы использовать средства сборки Apple, которые необходимы для компиляции приложений в машинном коде iOS. 
    - [Требования к системе Mac](~/cross-platform/get-started/requirements.md#macos-requirements)

## <a name="setup"></a>Установка

Процедура настройки для разработки Xamarin.iOS в Visual Studio 2017 включает следующие этапы.

1. Настройка Windows (установка Visual Studio 2017)

    Xamarin.iOS поддерживает выпуски Visual Studio 2017 Community, Professional и Enterprise на изолированном компьютере или виртуальной машине.
    
    - [Установите Visual Studio 2017](~/cross-platform/get-started/installation/windows.md).

2. Настройка Mac (установка Xcode и Visual Studio для Mac)

    Для сборки, отладки и подписывания приложений iOS для распространения Visual Studio 2017 необходим сетевой доступ к узлу сборки Mac, на котором настроены средства разработчика Apple (Xcode) и Xamarin.iOS.

    - [Скачайте и установите Xcode из магазина Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12). 
    - [Установите Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/installation), при этом также устанавливается Xamarin.iOS.

    > [!NOTE] 
    > Если вы не хотите устанавливать Visual Studio для Mac, начиная с [Visual Studio 2017 версии 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) поддерживается автоматическая настройка на узле сборки Mac программного обеспечения, необходимого для сборки приложений Xamarin.iOS. Дополнительные сведения см. в разделе [Автоматическая подготовка Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning).

3. Связывание с компьютером Mac (подключение Visual Studio 2017 к Mac)

    Чтобы в Visual Studio 2017 можно было использовать средства сборки iOS на компьютере Mac, необходимо действующее сетевое подключение.

    - [Прочитайте руководство по связыванию с компьютером Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="summary"></a>Сводка

В этой статье описывается настройка компьютера Windows и связанного узла сборки Mac для разработки Xamarin.iOS.

## <a name="next-steps"></a>Следующие шаги

- [Введение в Xamarin.iOS для Visual Studio](introduction-to-xamarin-ios-for-visual-studio.md)
- [Настройка Visual Studio 2017](config-options.md)
- [Подготовка устройств](~/ios/get-started/installation/device-provisioning/index.md)
