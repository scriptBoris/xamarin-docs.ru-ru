---
title: Возможности на платформе Android
description: В этой статье объясняется, как добавлять функции Android в приложения Xamarin.Forms.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2018
ms.openlocfilehash: 72a55441658755c1e068b33f910f3b7c0db5502f
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207782"
---
# <a name="android-platform-features"></a>Возможности на платформе Android

При разработке приложения Xamarin.Forms для Android требуется Visual Studio. [Страница "размер"](~/xamarin-forms/get-started/installation.md) содержит дополнительные сведения о необходимых компонентах.

## <a name="platform-specifics"></a>Особенности платформы

Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов.

Следующие функциональные возможности платформы предоставляется для представления Xamarin.Forms, страницы и макеты на устройстве Android:

- Управление Z-порядком визуальных элементов, чтобы определить порядок отображения. Дополнительные сведения см. в разделе [VisualElement повышение прав на устройстве Android](visualelement-elevation.md).
- Отключение режима устаревших цвет в поддерживаемой [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [VisualElement прежних версий цветовой режим на устройстве Android](legacy-color-mode.md).

Предоставляются следующие функциональные возможности платформы для представления Xamarin.Forms в Android:

- Используя Отбивка по умолчанию и значений тени Android кнопок. Дополнительные сведения см. в разделе [заполнения кнопки и теней в Android](button-padding-shadow.md).
- Настройка данный метод ввода параметров редактора для программируемой клавиатуры для [ `Entry` ](xref:Xamarin.Forms.Entry). Дополнительные сведения см. в разделе [параметры редактора метода ввода для записи в Android](entry-ime-options.md).
- Включение тени `ImageButton`. Дополнительные сведения см. в разделе [ImageButton Drop тени в Android](imagebutton-drop-shadow.md).
- Включение быстрой прокрутке в [ `ListView` ](xref:Xamarin.Forms.ListView) Дополнительные сведения см. в разделе [ListView быстрой прокрутке в Android](listview-fast-scrolling.md).
- Управление ли [ `WebView` ](xref:Xamarin.Forms.WebView) можно отобразить смешанное содержимое. Дополнительные сведения см. в разделе [WebView смешанного содержимого в Android](webview-mixed-content.md).

Следующие функциональные возможности платформы предоставляется для страницы Xamarin.Forms в Android:

- Значение высоты элемента на панели навигации на [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Дополнительные сведения см. в разделе [Высота отрезка NavigationPage в Android](navigationpage-bar-height.md).
- Отключение анимации перехода, при переходе по страницам в [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Дополнительные сведения см. в разделе [TabbedPage анимация перехода страницы в Android](tabbedpage-transition-animations.md).
- Включение считывания между страницами в [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Дополнительные сведения см. в разделе [TabbedPage страницы проведение пальцем по экрану в Android](tabbedpage-page-swiping.md).
- Установка инструментов расположение и цвет для [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Дополнительные сведения см. в разделе [TabbedPage размещения панели инструментов и цвета в Android](tabbedpage-toolbar-placement-color.md).

Предоставляются следующие функциональные возможности платформы для Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application) класс на устройствах Android:

- Настройка режима работы экранной клавиатуры. Дополнительные сведения см. в разделе [обратимо режиме ввода клавиатуры на Android](soft-keyboard-input-mode.md).
- Отключение [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) и [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) странице события жизненного цикла на приостановить и возобновить соответственно, для приложений, использующих AppCompat. Дополнительные сведения см. в разделе [событий жизненного цикла страницы в Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Поддержка платформ

Первоначально проект Xamarin.Forms Android по умолчанию используется старого стиля отрисовки элемента управления, общей до Android 5.0. Приложения, созданные с помощью шаблона имеют `FormsApplicationActivity` как базовый класс для их основного действия.

## <a name="material-design-via-appcompat"></a>Материальное через AppCompat

Xamarin.Forms Android проекты, использующие сейчас `FormsAppCompatActivity` как базовый класс для их основного действия. Этот класс использует **AppCompat** функциональные возможности, предоставляемые системой Android для реализации темы материальному дизайну.

Чтобы добавить материал разработчикам в проект Xamarin.Forms Android, выполните [поддерживают инструкции по установке для совместимости приложений](appcompat-material-design.md)

Вот **Todo** образец со значением по умолчанию `FormsApplicationActivity`:

[![](images/before-appcompat-sml.png "Пример списка дел приложения без AppCompat")](images/before-appcompat.png#lightbox "Todo пример приложения без параметров совместимости приложений")

И это тот же код после обновления проекта для использования `FormsAppCompatActivity` (и добавления сведений Дополнительные темы):

[![](images/post-appcompat-sml.png "TODO пример приложения с помощью AppCompat и тем")](images/post-appcompat.png#lightbox "Todo пример приложения с помощью AppCompat и темы")

> [!NOTE]
> При использовании `FormsAppCompatActivity`, [базовых классов для некоторых Android пользовательские отрисовщики](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) будет отличаться.

## <a name="related-links"></a>Связанные ссылки

- [Добавление поддержки материального исполнения](appcompat-material-design.md)
