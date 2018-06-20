---
title: Создание пользовательских интерфейсов с iOS конструктора
description: В этом документе описывается использование конструктора операций ввода-вывода для Xamarin для создания пользовательского интерфейса приложения с помощью раскадровки и .xib файлов. Ссылки на документы, которые обсуждаются средства доступности, его основные функциональные возможности, проектирование элементов управления и приводятся примеры его использования.
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/31/2018
ms.openlocfilehash: a931373a6abba3084af3c7aefcdddc903ad1b577
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209236"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>Создание пользовательских интерфейсов с iOS конструктора

_Конструктор Xamarin для операций ввода-вывода — это визуальный конструктор для iOS раскадровки и интерфейс построителя форматы, полностью интегрирован с Visual Studio для Mac и Visual Studio. IOS конструктор поддерживает полной совместимости с форматами раскадровки и .xib, поэтому файлы можно редактировать в Visual Studio для Mac или Visual Studio, в дополнение к Xcode интерфейс построителя. Кроме того конструктор Xamarin iOS поддерживает дополнительные функции, такие как пользовательские элементы управления, которые отображаются во время разработки в редакторе._

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![iOS конструктора в Visual Studio для Mac](images/designer-vsmac-sml.png "iOS конструктора")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Конструктор в Visual Studio iOS](images/designer-vs.png "iOS конструктора")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>Доступность

Конструктор Xamarin для операций ввода-вывода доступна в Visual Studio для Mac и в Visual Studio 2017 г. в Windows.

Эти руководства предполагается Знакомство с содержимым, охваченных [Xamarin.iOS Приступая к работе проводит](~/ios/get-started/index.md).

## <a name="ios-designer-basicsintroductionmd"></a>[Основные сведения о конструкторе iOS](introduction.md)

В этом руководстве описаны функции конструктора Xamarin iOS. Здесь рассмотрены основные конструктора, показывающий, как использовать конструктор для размещения элементов управления визуально и изменение свойств.

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[Обзор разработки элементов управления](ios-designable-controls-overview.md)

В этом руководстве выполняет поиск в глубину в пользовательские элементы управления, как они создаются и какие требования они должны соответствовать для отображения в области конструктора. Кроме того показано, как отлаживать распространенных проблем, которые могут возникнуть при использовании разработки элементов управления.

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[Пошаговое руководство. Использование пользовательских элементов управления с iOS конструктора](ios-designable-controls-walkthrough.md)

Эта статья содержит пошаговое руководство, в котором показано, как создать пользовательский элемент управления и использовать его в конструкторе операций ввода-вывода. В этом примере показано создание элемента управления в панели элементов конструктора, его можно перетаскивания или перетащить в представление. Кроме того показано, как реализовать элемент управления, отображается правильно во время разработки и среды выполнения, а также создание свойства, которые могут быть установлены во время разработки.

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[Автоматический макет с Xamarin iOS конструктора](designer-auto-layout.md)

В этом руководстве описаны iOS автоматический макет и нового ограничения рабочего процесса доступными в конструкторе операций ввода-вывода.
