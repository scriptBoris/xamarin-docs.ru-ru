---
title: Удаленный симулятор iOS для Windows
description: Удаленный iOS Simulator для Windows дает возможность тестирования приложений на симуляторе iOS, отображаются в окнах вместе с Visual Studio 2017 г.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: topgenorth
ms.author: toopge
ms.date: 05/11/2018
ms.openlocfilehash: b07cc24e63f4aa3ce4451e3bdb5819f1df1058c6
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34149811"
---
# <a name="remoted-ios-simulator-for-windows"></a>Удаленный симулятор iOS для Windows

Удаленный iOS Simulator для Windows дает возможность тестирования приложений на симуляторе iOS, отображаются в окнах вместе с Visual Studio 2017 г.

[![](ios-simulator-images/hero-sml.png "Симулятор iOS под управлением Windows")](ios-simulator-images/hero.png#lightbox)

## <a name="getting-started"></a>Начало работы

Имитатор удаленных операций ввода-вывода для Windows устанавливается автоматически в рамках Xamarin в Visual Studio 2017 г. Чтобы использовать его, выполните следующие действия.

1. [Выполнить сопряжение Visual 2017 г. узел построения Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. В Visual Studio 2017 г. Запустите отладку проекта iOS или tvOS. Имитатор удаленных операций ввода-вывода для Windows будет отображаться на компьютере Windows.

## <a name="simulator-window"></a>Окна имитатора

Панели инструментов в верхней части окна имитатора содержит ряд полезных кнопок:

- **Домашняя** — имитирует кнопка «Главная» на устройстве iOS
- **Блокировка** — блокирует имитатор (проведите для разблокировки)
- **Снимок экрана** — сохраняет снимок экрана симулятора
- [**Параметры** ](#settings) — отображает клавиатуры, расположение и другие параметры
- [**Другие параметры** ](#other-options) — различные параметры симулятора, например смены и встряхивания жесты на экране появится

    [![](ios-simulator-images/maps-app-sml.png "пример сопоставляет симулятор iOS")](ios-simulator-images/maps-app.png#lightbox)

## <a name="settings"></a>Параметры

Панели инструментов щелкните значок шестеренки открывают **параметры** окна:

[![](ios-simulator-images/settings-sml.png "Параметры симулятора iOS")](ios-simulator-images/settings.png#lightbox)

Эти параметры позволяют включить аппаратной клавиатуре, выберите расположение, устройство необходимо включить Touch ID отчета (статические и перемещения расположения поддерживаются) и сбросить содержимое и параметры для симулятора.

## <a name="other-options"></a>Другие параметры

Панели инструментов кнопку с многоточием вы увидите еще другие параметры, такие как поворот, встряхивания жестов и перезагрузки. Эти же параметры можно рассматривать как список, щелкните правой кнопкой мыши в любом месте окна имитатора:

[![](ios-simulator-images/more-sml.png "Дополнительные параметры для симулятора iOS")](ios-simulator-images/more.png#lightbox)

## <a name="touchscreen-support"></a>Поддержка сенсорного экрана

Большинство современных компьютеров Windows имеют сенсорных экранов. Поскольку удаленной iOS Simulator для Windows поддерживает взаимодействие сенсорный ввод, можно протестировать приложение с же жестом сжатия, считывание и несколькими пальцем сенсорный ввод, которые доступны для физических устройствах iOS.

Аналогичным образом удаленной iOS Simulator для Windows обрабатывает ввод от пера Windows как Apple карандаша входных данных.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Отключение удаленной iOS Simulator для Windows

Чтобы отключить удаленное iOS Simulator для Windows, перейдите в папку **Сервис > Параметры > Xamarin > Параметры iOS** и снимите флажок **удаленного симулятор для Windows**.

[![](ios-simulator-images/options-sml.png "флажок, чтобы использовать симулятор")](ios-simulator-images/options.png#lightbox)

Этот параметр отключен, отладка откроется симуляторе iOS на подключенном Mac построения узла.
