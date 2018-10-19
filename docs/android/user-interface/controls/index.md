---
title: (Widgets) управляет Android
description: Стандартные блоки для создания пользовательских интерфейсов Xamarin.Android
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 842fb1df2c9cc1aaf1a106687179a3730c2503bd
ms.sourcegitcommit: 7e4070bc104d612b6754ea35dd5a49c5c3d45f4a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "32436717"
---
# <a name="android-controls-widgets"></a>(Widgets) управляет Android

Xamarin.Android предоставляет все собственные элементы управления пользовательского интерфейса (мини-приложения), предоставляемые Android. Эти элементы управления можно легко добавить для приложений Xamarin.Android с помощью Android Designer, или программным путем с помощью XML-файлов разметки. Независимо от того, какой метод выбран Xamarin.Android предоставляет все свойства объекта пользовательского интерфейса и методов в C#. В следующих разделах вводят наиболее распространенных элементов управления интерфейса пользователя для Android и объясняется, как включить их в приложениях Xamarin.Android.

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[Панели действий](~/android/user-interface/controls/action-bar.md) 

`ActionBar` — Это панель, отображающий название действия, интерфейсы навигации и другие интерактивные элементы. Как правило на панели действий отображается в верхней части окна действия.

![Пример панели действий](images/action-bar.png)


## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[Автозаполнение](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` является элементом представления ввода и редактирования текста, который автоматически показывает предлагаемые варианты завершения, пока пользователь вводит текст. В раскрывающемся меню, из которого пользователь может выбрать элемент для замены содержимого поля ввода с отображается список предложений.

![Пример того, автоматическое завершение](images/auto-complete.png)


## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[Кнопки](~/android/user-interface/controls/buttons/index.md)

Кнопки являются элементами пользовательского интерфейса, которые пользователь нажимает для выполнения действия.

![Пример кнопки](images/buttons.png)


## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendar](~/android/user-interface/controls/calendar.md)

`Calendar` Класс используется для преобразования конкретного экземпляра в времени (значение миллисекунды, являющийся смещением от начала отсчета времени) для значения, такие как год, месяц, час, день месяца и даты создания следующей недели.
`Calendar` поддерживает множество возможностей взаимодействия с данными календаря, включая возможность чтения и записи событий, участников и напоминания. С помощью поставщика календаря в приложении, данные, добавляемые через API-Интерфейс будет отображаться в приложении встроенный календарь, который поставляется с Android.

![Пример календаря](images/calendar.png)


## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` является компонентом пользовательского интерфейса, который представляет содержимое text и image в представлениях, которые напоминают карты. `CardView` реализуется в виде `FrameLayout` мини-приложение со скругленными углами и тени. Как правило `CardView` используется для представления элемента одной строки в `ListView` или `GridView` группа «Просмотр».

![Пример представления карты](images/cardview.png)


## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[Изменить текст](~/android/user-interface/controls/edit-text.md)

`EditText` — Это элемент пользовательского интерфейса, который используется для ввода и изменения текста.

![Пример редактирования текста](images/edit-text.png)


## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[Коллекция](~/android/user-interface/controls/gallery.md)

`Gallery` является мини-приложения макета, который используется для отображения элементов в список, горизонтальной прокруткой он помещает текущее выделение в центре представления.

![Коллекция примеров](images/gallery.png)


## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[Панель навигации](~/android/user-interface/controls/navigation-bar.md)

*Панель навигации* предоставляет элементы управления навигацией на устройствах, не содержащих кнопок для **Главная**, **обратно**, и **меню**.

![Пример панели навигации](images/navigation-bar.png)


## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[Средства выбора](~/android/user-interface/controls/pickers/index.md)

*Выбор* являются элементами пользовательского интерфейса, которые позволяют пользователю выбрать дату или время с помощью диалоговых окон, предоставляемых системой Android.

![Пример выбора](images/picker.png)


## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[Всплывающее меню](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` используется для отображения всплывающего меню, вложенные в это конкретное представление.

![Пример всплывающего меню](images/popup-menu.png)


## <a name="ratingbarandroiduser-interfacecontrolsratingbarmd"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

Объект `RatingBar` — это элемент пользовательского интерфейса, который отображает оценку в звезды.

![Пример RatingBar](ratingbar-images/01-ratingbar.png)


## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[Вертушка](~/android/user-interface/controls/spinner.md)

`Spinner` — Это элемент пользовательского интерфейса, который предоставляет быстрый способ выбрать одно значение из набора. Это simmilar для раскрывающегося списка. 

![Пример "Счетчик"](images/spinner.png)


## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[Коммутатор](~/android/user-interface/controls/switch.md)

`Switch` — Это элемент пользовательского интерфейса, который позволяет пользователю переключаться между двумя состояниями, как в или ВЫКЛЮЧАЕТСЯ. `Switch` Значение по умолчанию — OFF.

![Пример коммутатора](images/switch.png)


## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` — Это представление, который использует Двухмерная отрисовка с аппаратным ускорением для включения видео или OpenGL поток содержимого для отображения.

![Пример представления текстуры](images/texture-view.png)


## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

`Toolbar` Мини-приложения (впервые представлено в Android 5.0 Lollipop) может рассматриваться как обобщением интерфейса панели действий &ndash; он предназначен для замены на панели действий. `Toolbar` Может использоваться в любом месте в макете приложения и гораздо более возможность настройки, чем панель действий.

![Пример панели инструментов](images/toolbar.png)


## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

`ViewPager` — Это диспетчер макета, позволяет перевернуть слева и справа по страницам данных.

![Пример ViewPager](images/viewpager.png)


## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` — Это элемент пользовательского интерфейса, который позволяет создавать собственные окна просмотра веб-страниц (или даже разрабатывать полный браузера).

![Пример веб-представление](images/web-view.png)

