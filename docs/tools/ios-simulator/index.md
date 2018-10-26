---
title: Удаленный симулятор iOS для Windows
description: Удаленный iOS Simulator для Windows позволяет тестировать свои приложения в симуляторе iOS в Windows вместе с Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: lobrien
ms.author: laobri
ms.date: 05/11/2018
ms.openlocfilehash: c923a62916959c74b8cd753d25361afee68e75fe
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131935"
---
# <a name="remoted-ios-simulator-for-windows"></a>Удаленный симулятор iOS для Windows

Удаленный iOS Simulator для Windows позволяет тестировать свои приложения в симуляторе iOS в Windows вместе с Visual Studio 2017.

[![](images/hero-sml.png "Симулятор iOS под управлением Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Начало работы

Удаленный iOS Simulator для Windows устанавливается автоматически как часть Xamarin в Visual Studio 2017. Чтобы использовать его, выполните следующие действия.

1. [Сопряжение Visual Studio 2017 к узлу сборки Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. В Visual Studio 2017 запустите отладку в проект iOS или tvOS. Удаленный iOS Simulator для Windows будет отображаться на компьютере Windows.

Контрольные значения [в этом видео](deploy.md) Пошаговое руководство.

## <a name="simulator-window"></a>Окно симулятора

Панели инструментов в верхней части окна имитатора содержит ряд полезных кнопки:

- **Домашний** — имитирует кнопка "Главная" на устройстве iOS
- **Блокировка** — блокирует симулятор (жеста перетаскивания для разблокировки)
- **Снимок экрана** — сохраняет снимок экрана симулятора
- [**Параметры** ](#settings) — отображает клавиатуры, расположение и другие параметры
- [**Другие варианты** ](#other-options) — откроется симулятор различные действия, например жестов поворота и встряхивания

    [![](images/maps-app-sml.png "пример сопоставляет симулятор iOS")](images/maps-app.png#lightbox)

## <a name="settings"></a>Параметры

Если щелкнуть значок шестеренки панели инструментов, откроется **параметры** окна:

[![](images/settings-sml.png "Параметры симулятора iOS")](images/settings.png#lightbox)

Эти параметры позволяют включить аппаратную клавиатуру, выберите расположение, устройство должно отчета (статические и скользящего расположений поддерживаются), включите Touch ID и сбросить содержимое и параметры для симулятора.

## <a name="other-options"></a>Другие параметры

На панели инструментов кнопку с многоточием показывает другие параметры, такие как поворот, жесты встряхивания и перезагрузки. Эти же параметры можно рассматривать как список, щелкнув правой кнопкой мыши окно симулятора:

[![](images/more-sml.png "Дополнительные параметры симулятора iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Поддержка сенсорного экрана

Большинство современных компьютеров Windows имеют сенсорные экраны. Так как удаленный iOS Simulator для Windows поддерживает работу, можно протестировать приложение с тем же жестом сжатия, проведите по экрану и несколькими пальцами сенсорные жесты, используемые с физических устройств с iOS.

Точно так же удаленный iOS Simulator для Windows обрабатывает ввод от пера Windows как Apple Карандаш входных данных.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Отключение удаленный iOS Simulator для Windows

Чтобы отключить удаленный iOS Simulator для Windows, перейдите к **Сервис > Параметры > Xamarin > Параметры iOS** и снимите флажок **удаленный Simulator для Windows**.

[![](images/options-sml.png "флажок, чтобы использовать симулятор")](images/options.png#lightbox)

Этот параметр отключен, отладка откроется симулятора iOS на подключенном компьютере Mac узла сборки.
