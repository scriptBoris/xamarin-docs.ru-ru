---
title: Android элементов управления (мини-приложений)
description: Строительными блоками при создании Xamarin.Android пользовательские интерфейсы
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 28418c3b3fedcd24963008eb3b59ffa782d791f1
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436717"
---
# <a name="android-controls-widgets"></a>Android элементов управления (мини-приложений)

Xamarin.Android предоставляет все собственные элементы управления пользовательского интерфейса (мини-приложений), предоставляемые Android. Эти элементы управления можно легко добавить Xamarin.Android приложений с помощью конструктора Android или программным путем с помощью разметки XML-файлов. Независимо от выбранного метода Xamarin.Android предоставляет все свойства объекта пользовательского интерфейса и методов в C#. В следующих разделах вводят наиболее распространенных элементов управления интерфейса пользователя для Android и объясняется, как интегрировать в приложения Xamarin.Android.

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[Панели действий](~/android/user-interface/controls/action-bar.md) 

`ActionBar` Представляет панель инструментов, которая отображает название действия, навигации интерфейсы и другие интерактивные элементы. Как правило панель действий отображается в верхней части окна действия.

![Пример панели действий](images/action-bar.png)


## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[Автозаполнение](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` — Это элемент представление для редактирования текста, показывающая завершение предложения автоматически во время ввода пользователя. В раскрывающемся меню, из которого пользователь может выбрать элемент для замены содержимого поля ввода с отображается список предложений.

![Пример автозавершение](images/auto-complete.png)


## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[Кнопки](~/android/user-interface/controls/buttons/index.md)

Кнопки, элементы пользовательского интерфейса, пользователь нажимает кнопку для выполнения действия.

![Пример кнопки](images/buttons.png)


## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendar](~/android/user-interface/controls/calendar.md)

`Calendar` Класс используется для преобразования в конкретный экземпляр времени (значение миллисекунды, смещение от начала эпохи) для значения, такие как год, месяц, час, день месяца и даты создания следующей неделе.
`Calendar` поддерживает широкий набор параметров взаимодействия с данными календаря, включая возможность считывать и записывать события, участники и напоминания. С помощью календаря поставщика в приложении, данных, которые добавляются с помощью API-интерфейса будет отображаться в приложении встроенный календарь, входящий в состав Android.

![Пример календаря](images/calendar.png)


## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` — это компонент пользовательского интерфейса, которая представляет содержимое text и image в представлениях, которые похожи на картах. `CardView` реализуется в виде `FrameLayout` мини-приложение со скругленными углами и тени. Как правило `CardView` используется для представления элемента одной строки в `ListView` или `GridView` группа «Просмотр».

![Пример представления карты](images/cardview.png)


## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[Изменение текста](~/android/user-interface/controls/edit-text.md)

`EditText` — Это элемент пользовательского интерфейса, который используется для ввода и изменения текста.

![Пример редактирования текста](images/edit-text.png)


## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[Коллекция](~/android/user-interface/controls/gallery.md)

`Gallery` Представляет макет графического элемента, который используется для отображения элементов в списке горизонтальной прокруткой; он помещает текущее выделение в центре представления.

![Пример коллекции](images/gallery.png)


## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[Панель навигации](~/android/user-interface/controls/navigation-bar.md)

*Панель навигации* предоставляет элементы управления навигацией на устройствах, которые не включают кнопок для **Главная**, **обратно**, и **меню**.

![Пример панели навигации](images/navigation-bar.png)


## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[Средства выбора](~/android/user-interface/controls/pickers/index.md)

*Выбор* являются элементы пользовательского интерфейса, которые позволяют пользователю выбрать дату или время с помощью диалоговых окон, предоставляемых системой Android.

![Пример выбора](images/picker.png)


## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[Всплывающее меню](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` используется для отображения всплывающего меню, прикрепленных к конкретному представлению.

![Пример всплывающего меню](images/popup-menu.png)


## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[Вертушка](~/android/user-interface/controls/spinner.md)

`Spinner` — Это элемент пользовательского интерфейса, который предоставляет быстрый способ выбора одно значение из набора. Это simmilar в раскрывающемся списке. 

![Пример "Счетчик"](images/spinner.png)


## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[Коммутатор](~/android/user-interface/controls/switch.md)

`Switch` — Это элемент пользовательского интерфейса, который позволяет пользователю переключаться между двумя состояниями, как в или OFF. `Switch` Значение по умолчанию — OFF.

![Пример коммутатора](images/switch.png)


## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` является представлением, которое использует аппаратное ускорение двухмерной отрисовки для включения видео или OpenGL поток содержимого для отображения.

![Пример представления текстуры](images/texture-view.png)


## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

`Toolbar` Мини-приложения (поддерживается Android 5.0 без описания операций) можно рассматривать как обобщением интерфейса панели действий &ndash; предназначен для замены панели действий. `Toolbar` Может использоваться в любом месте в структуру приложения, и гораздо более возможность настройки, чем в панели действий.

![Пример панели инструментов](images/toolbar.png)


## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

`ViewPager` Представляет диспетчер макет, который позволяет пользователю перемещаться влево и вправо по страницам данных.

![Пример ViewPager](images/viewpager.png)


## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` — Это элемент пользовательского интерфейса, который позволяет вам создать собственную окно для просмотра веб-страниц (или даже создавать полный браузера).

![Пример веб-представление](images/web-view.png)

