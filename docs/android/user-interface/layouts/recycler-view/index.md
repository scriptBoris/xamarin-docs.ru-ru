---
title: RecyclerView
description: RecyclerView — это группа представление для отображения коллекций. она должна быть более гибкий замены старой представление групп, таких как ListView и GridView.  В этом руководстве объясняется, как использовать и настраивать RecyclerView Xamarin.Android приложений.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/03/2018
ms.openlocfilehash: 187339244d53c154cc22672a3d2ceba7e0a75bcf
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766339"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView — это группа представление для отображения коллекций. она должна быть более гибкий замены старой представление групп, таких как ListView и GridView.  В этом руководстве объясняется, как использовать и настраивать RecyclerView Xamarin.Android приложений._

## <a name="recyclerview"></a>RecyclerView

Во многих приложениях необходимо показывать коллекции того же типа (например, сообщения, контакты, изображения или песни); часто эта коллекция является слишком большой для отображения на экране, поэтому коллекции представлены в небольшое окно, которое можно плавно прокручивать все элементы в коллекции.
`RecyclerView` является мини-приложение Android, отображающий коллекцию элементов в списке или сетке, позволяя пользователям выполнять прокрутку по коллекции. Ниже приведен снимок экрана примера приложения, использующего `RecyclerView` для отображения содержимого папки "Входящие" для электронной почты в список с вертикальной прокруткой:

[![Пример приложения с помощью RecyclerView список входящих сообщений](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` предлагает два замечательных функций:

-  Он имеет гибкую архитектуру, позволяет изменять поведение путем подключения в предпочтительный компонентах.

-  Это эффективно с большими коллекциями, так как он повторно использует представления элемента и требует использования *просмотра владельцев* для кэширования ссылок на представление.

В этом руководстве описывается использование `RecyclerView` в приложениях Xamarin.Android; здесь объясняется, как добавить `RecyclerView` пакета для проекта Xamarin.Android, который описывает как `RecyclerView` функции в типичном приложении. Реальный код примеры показывают, как интегрировать `RecyclerView` в приложение, как реализовать щелкните представление элемента, как обновить `RecyclerView` при изменении базовых данных. В этом руководстве предполагается, что вы знакомы с Xamarin.Android разработки.


### <a name="requirements"></a>Требования

Несмотря на то что `RecyclerView` — часто, связанный с Android 5.0 без описания операций, предлагается библиотека поддержки &ndash; `RecyclerView` работают с приложениями для этого целевого API уровня 7 (Android 2.1) и более поздних версий. Для использования требуются следующие `RecyclerView` в приложениях на базе Xamarin:

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 или более поздней версии необходимо установить и настроить с помощью Visual Studio или Visual Studio для Mac.

-  Проект приложения должен содержать **Xamarin.Android.Support.v7.RecyclerView** пакета. Дополнительные сведения об установке пакетов NuGet см. в разделе [Пошаговое руководство: включая NuGet в проекте](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


### <a name="overview"></a>Обзор

`RecyclerView` можно рассматривать как замену `ListView` и `GridView` мини-приложения в Android. Подобно своим предшественникам, `RecyclerView` предназначена для отображения большого количества данных в небольшом окне, но `RecyclerView` предоставляет дополнительные возможности макета и лучше оптимизирован для отображения больших коллекций. Если вы знакомы с `ListView`, существует несколько важных различий между `ListView` и `RecyclerView`:

-   `RecyclerView` несколько более сложен для использования: необходимо написать дополнительный код для использования `RecyclerView` по сравнению с `ListView`.

-   `RecyclerView` не предоставляет предопределенные адаптера; необходимо реализовать код адаптера, который обращается к источнику данных. Однако Android включает несколько стандартных адаптеров, которые работают с `ListView` и `GridView`.

-   `RecyclerView` не предоставляет элемента щелкните событие, когда пользователь касается элемента; Вместо этого щелкните элемент события обрабатываются вспомогательных классов. В отличие от этого `ListView` предлагает события click элемента.

-   `RecyclerView` повышает производительность путем перезапуска представления и применяя шаблон владельца представления, который позволяет избежать ненужных макета обращений к ресурсу. Использование шаблона представление владельца является необязательным в `ListView`.

-   `RecyclerView` основан на модульной конструкции, для упрощения настройки. Например можно подключить другой макет политики без значительное изменение кода в приложение.
    В отличие от этого `ListView` является относительно монолитные в структуре.

-   `RecyclerView` включает встроенные анимационные ролики для элемента, добавления и удаления. `ListView` анимация требует некоторых дополнительных усилий со стороны разработчика приложений.


### <a name="sections"></a>Разделы

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Части RecyclerView и функциональные возможности](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

В этом разделе объясняется, как `Adapter`, `LayoutManager`, и `ViewHolder` работают в одном месте, как вспомогательные классы для поддержки `RecyclerView`.
Он представлен общий обзор каждого из этих вспомогательных классов и объясняется, как их использовать в приложении.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Простой пример RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

В этом разделе основан на информации из [RecyclerView части и функциональные возможности](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) , предоставляя реальные примеры того, как различные `RecyclerView` элементы реализуются для выполнения сборки приложения просмотра фотографий реального мира.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[В примере RecyclerView расширение](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

В этом разделе добавляет дополнительный код для примера приложения, представленных в [простой пример RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) чтобы продемонстрировать, как обрабатывать событие щелчка элемента и обновлять `RecyclerView` при изменении источника базовых данных.


### <a name="summary"></a>Сводка

В этом руководстве представлена Android `RecyclerView` мини-приложения; оно описано добавление `RecyclerView` поддержки библиотеки в проекты Xamarin.Android, как `RecyclerView` перезапускается представления, как он реализует шаблон владельца представления для повышения эффективности и как различные вспомогательные классы, которые составляют `RecyclerView` совместной работы для отображения коллекций. Он предоставляется пример кода для демонстрации как `RecyclerView` интегрирован в приложение, оно описано как построить `RecyclerView`макет политики путем подключения в различные виды диаграмм руководителей и описано, как обрабатывать элемент выберите события и уведомления `RecyclerView`изменения источника данных.

Дополнительные сведения о `RecyclerView`, в разделе [ссылку на класс RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).


## <a name="related-links"></a>Связанные ссылки

- [RecyclerViewer (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Общие сведения о без описания операций](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
