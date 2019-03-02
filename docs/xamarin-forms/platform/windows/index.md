---
title: Функции платформы Windows
description: 'В этой статье описывается поддержка платформ Windows, которая доступна в Xamarin.Forms.'
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
---

# <a name="windows-platform-features"></a>Функции платформы Windows

При разработке приложения Xamarin.Forms для платформ Windows требуется Visual Studio. [Страница "размер"](~/get-started/requirements.md) содержит дополнительные сведения о необходимых компонентах.

![](images/allhanselman.png "Приложения Xamarin.Forms, работающие на Windows")

## <a name="platform-specifics"></a>Особенности платформы

Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов.

Предоставляются следующие функциональные возможности платформы для представления Xamarin.Forms, страницы и макеты на универсальной платформе Windows (UWP):

- Установка ключа доступа для [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [VisualElement ключи доступа в Windows](#visualelement-accesskeys).
- Отключение режима устаревших цвет в поддерживаемой [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Дополнительные сведения см. в разделе [VisualElement прежних версий цветовой режим на Windows](#legacy-color-mode).

Следующие функциональные возможности платформы предоставляется для представления Xamarin.Forms для UWP:

- Обнаружение, порядок чтения из текстового содержимого в [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), и [ `Label` ](xref:Xamarin.Forms.Label) экземпляров. Дополнительные сведения см. в разделе [InputView порядок чтения на Windows](#inputview-readingorder).
- Включение поддержки жест касания в [ `ListView` ](xref:Xamarin.Forms.ListView). Дополнительные сведения см. в разделе [ListView SelectionMode в Windows](#listview-selectionmode).
- Включение [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) для взаимодействия с подсистемой проверки орфографии. Дополнительные сведения см. в разделе [SearchBar проверку орфографии Windows](#searchbar-spellcheck).
- Включение [ `WebView` ](xref:Xamarin.Forms.WebView) на отображение оповещений JavaScript в диалоговом окне сообщения универсальной платформы Windows. Дополнительные сведения см. в разделе [WebView предупреждения JavaScript на Windows](#webview-javascript-alert).

Следующие функциональные возможности платформы предоставляется для страницы Xamarin.Forms для UWP:

- Свертывание [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) панели навигации. Дополнительные сведения см. в разделе [MasterDetailPage панели навигации в Windows](#collapsable_navigation_bar).
- Настройка параметров размещения панели инструментов. Дополнительные сведения см. в разделе [расположение панели инструментов страницы на Windows](#toolbar_placement).
- Включение значков страниц, отображаемых на [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) панели инструментов. Дополнительные сведения см. в разделе [TabbedPage значки на Windows](#tabbedpage-icons).

## <a name="platform-support"></a>Поддержка платформ

Шаблоны Xamarin.Forms, доступные в Visual Studio, содержат проект универсальной платформы Windows (UWP).

> [!NOTE]
> Поддержка версий 1.x и 2.x Xamarin.Forms _Windows Phone 8 Silverlight_, _Windows Phone 8.1_, и _Windows 8.1_ разработки приложений. Тем не менее эти типы проектов стали нерекомендуемыми.

## <a name="getting-started"></a>Начало работы

Перейдите к **файл > Создать > проект** в Visual Studio и выберите один из **кросс-платформенных > пустое приложение (Xamarin.Forms)** шаблоны, чтобы приступить к работе.

Старые решения Xamarin.Forms, или были созданы в macOS, не будет иметь все проекты Windows, перечисленных выше (но их необходимо вручную добавить). Если на платформу Windows, необходимо еще в решении, посетите [инструкции по установке](installation/index.md) для добавления нужного Windows проект типа в секунду.

## <a name="samples"></a>Примеры

[Все примеры](https://github.com/xamarin/xamarin-forms-book-preview-2) для книга Чарльза Петцольда [ *Создание мобильных приложений с помощью Xamarin.Forms* ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) включают в себя проекты универсальной платформы Windows (для Windows 10).

[«Scott Hanselman» демонстрационное приложение](https://github.com/jamesmontemagno/Hanselman.Forms) доступна отдельно, а также включают проекты Apple Watch и Android Wear (соответственно с помощью Xamarin.iOS и Xamarin.Android, Xamarin.Forms не выполняется на этих платформах).

## <a name="related-links"></a>Связанные ссылки

- [Проекты установки Windows](~/xamarin-forms/platform/windows/installation/index.md)
