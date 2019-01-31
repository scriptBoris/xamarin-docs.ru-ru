---
title: Возможности на платформе iOS
description: Добавление функциональных возможностей для отдельных операций ввода-вывода в приложения Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2018
ms.openlocfilehash: 83946929874ae37caab459eed970c50b74154d66
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292341"
---
# <a name="ios-platform-features"></a>Возможности на платформе iOS

При разработке приложения Xamarin.Forms для iOS требуется Visual Studio. [Страница "размер"](~/get-started/installation.md) содержит дополнительные сведения о необходимых компонентах.

## <a name="platform-specifics"></a>Особенности платформы

Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов.

Следующие функциональные возможности платформы предоставляется для представления Xamarin.Forms, страниц и макетов в iOS:

- Размытие поддержки для любого [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [VisualElement размытия в iOS](visualelement-blur.md).
- Отключение режима устаревших цвет в поддерживаемой [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [VisualElement прежних версий цветовой режим в iOS](legacy-color-mode.md).
- Включение тень [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [VisualElement Drop тенями iOS](visualelement-drop-shadow.md).

Предоставляются следующие функциональные возможности платформы для представления Xamarin.Forms в iOS:

- Обеспечение, введена текст встраивается в [ `Entry` ](xref:Xamarin.Forms.Entry) , корректируя размер шрифта. Дополнительные сведения см. в разделе [размер шрифта записи в iOS](entry-font-size.md).
- Задание цвета курсора [ `Entry` ](xref:Xamarin.Forms.Entry). Дополнительные сведения см. в разделе [цвет курсора запись в iOS](entry-cursor-color.md).
- Установка стиль разделителя [ `ListView` ](xref:Xamarin.Forms.ListView). Дополнительные сведения см. в разделе [стиль разделителя ListView на iOS](listview-separator-style.md).
- Управление, когда происходит выбор элементов в [ `Picker` ](xref:Xamarin.Forms.Picker). Дополнительные сведения см. в разделе [Выбор элемента в iOS](picker-selection.md).
- Включение [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) свойство для задания можно просмотреть, выбрав для положения на [ `Slider` ](xref:Xamarin.Forms.Slider) панели, а не по необходимости перетащите `Slider` бегунка. Дополнительные сведения см. в разделе [ползунок Thumb коснуться iOS](slider-thumb.md).

Следующие функциональные возможности платформы предоставляется для страницы Xamarin.Forms в iOS:

- Скрытие разделителя панели навигации на [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Дополнительные сведения см. в разделе [разделителя панели NavigationPage в iOS](navigation-bar-separator.md).
- Управление, является ли прозрачные панели навигации. Дополнительные сведения см. в разделе [полупрозрачность панель навигации в iOS](navigation-bar-translucent.md).
- Управление цветом ли текст строки состояния на [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) настраивается в соответствии с яркость панели навигации. Дополнительные сведения см. в разделе [режим цвет текста панели NavigationPage в iOS](status-bar-text-color.md).
- Управление, отображается ли заголовок страницы как крупным заголовком на панели навигации страницы. Дополнительные сведения см. в разделе [крупных названий страниц в iOS](page-large-title.md).
- Установка видимость полосы состояния [ `Page` ](xref:Xamarin.Forms.Page). Дополнительные сведения см. в разделе [видимость полосы состояние страницы в iOS](page-status-bar-visibility.md).
- Обеспечение содержимое этой страницы находится на области экрана, которое безопасно для всех устройств iOS. Дополнительные сведения см. в разделе [безопасном направляющая макета области в iOS](page-safe-area-layout.md).
- Настройка стиля представления модальные страницы на iPad. Дополнительные сведения см. в разделе [iPad стиль презентации модальные страницы](ipad-page-presentation-style.md).

Следующие функциональные возможности платформы предоставляется для макеты Xamarin.Forms в iOS:

- Управление ли [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) жест касания обрабатывает или передает его на его содержимое. Дополнительные сведения см. в разделе [ScrollView содержимого штрихи в iOS](scrollview-content-touches.md).

Предоставляются следующие функциональные возможности платформы для Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application) класс на устройствах iOS:

- Включение структурой элементов управления и Подготовка к просмотру обновлений, выполняемых в основном потоке. Дополнительные сведения см. в разделе [основной поток управления обновлений на iOS](main-thread-updates-ui.md).
- Включение [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) в области прокрутки для записи и совместно использовать жест pan с представления с прокруткой. Дополнительные сведения см. в разделе [одновременных распознавания жестов Pan в iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>форматирование конкретных операций ввода-вывода

Xamarin.Forms позволяет межплатформенный пользовательский интерфейс стили и цвета, чтобы задать -, но существуют другие параметры для настройки темы iOS с помощью API платформ в проекте iOS.

[Дополнительные сведения](formatting.md) о форматировании пользовательский интерфейс, с помощью API-интерфейсам конкретной операций ввода-вывода, например **Info.plist** конфигурации и `UIAppearance` API.

![](images/status-white-sml.png "iOS темы")

## <a name="other-ios-features"></a>Другие функции iOS

С помощью [пользовательские отрисовщики](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)и [помощью MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), его можно интегрировать широкий спектр собственных функций в Приложения Xamarin.Forms для iOS.
