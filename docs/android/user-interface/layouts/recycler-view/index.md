---
title: RecyclerView
description: RecyclerView — это группа представление для отображения коллекций; она должна быть более гибким заменяет старые Просмотр групп, таких как ListView и GridView.  В этом руководстве объясняется, как использовать и настраивать RecyclerView в приложениях Xamarin.Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/03/2018
ms.openlocfilehash: 1332a7ef5b8e5bb2f1178582bcf058123f1e177c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110151"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView — это группа представление для отображения коллекций; она должна быть более гибким заменяет старые Просмотр групп, таких как ListView и GridView.  В этом руководстве объясняется, как использовать и настраивать RecyclerView в приложениях Xamarin.Android._

## <a name="recyclerview"></a>RecyclerView

Многим приложениям требуется отобразить коллекции из одного типа (например, сообщения, контакты, изображения или песни); часто эту коллекцию помещаются на экране, представлены в небольшое окно плавной прокрутки по всем элементам в коллекции, коллекции.
`RecyclerView` — Это мини-приложение Android, который отображает коллекцию элементов в списке или сетке, позволяя пользователю выполнять прокрутку по коллекции. Ниже приведен снимок экрана примера приложения, использующего `RecyclerView` для отображения содержимого папки "Входящие" электронной почты в список с вертикальной прокруткой:

[![Пример приложения с помощью RecyclerView для папки "Входящие" списка сообщений](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` предлагает два интересных функций:

-  Он имеет гибкую архитектуру, которая позволяет изменять его поведение с подключением к предпочтительной компонентов.

-  Это эффективно с большими коллекциями, так как он повторно использует представления элемента и требует применения *Просмотр владельцев* для кэширования ссылок на представление.

В этом руководстве описывается использование `RecyclerView` в приложениях Xamarin.Android; здесь объясняется, как добавить `RecyclerView` пакета в проект Xamarin.Android и он описывается как `RecyclerView` функции в типичном приложении. Реальный код приведены примеры Показать, как интегрировать `RecyclerView` в приложении, как реализовать элемент представлению, щелкните и как обновить `RecyclerView` при изменении базовых данных. В этом руководстве предполагается, что вы знакомы с разработкой приложений Xamarin.Android.


### <a name="requirements"></a>Требования

Несмотря на то что `RecyclerView` — часто ассоциируемый с Android 5.0 Lollipop, он может служить библиотека поддержки &ndash; `RecyclerView` работает с приложениями этого целевой API уровня 7 (Android 2.1) и более поздних версий. Следующие необходим для использования `RecyclerView` в приложениях на базе Xamarin:

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 или более поздней версии необходимо установить и настроить Visual Studio или Visual Studio для Mac.

-  Проект приложения должен включать **Xamarin.Android.Support.v7.RecyclerView** пакета. Дополнительные сведения об установке пакетов NuGet см. в разделе [Пошаговое руководство: включая NuGet в проекте](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


### <a name="overview"></a>Обзор

`RecyclerView` может рассматриваться как замену `ListView` и `GridView` мини-приложения в Android. Подобно своим предшественникам, `RecyclerView` предназначена для отображения большого набора данных в небольшом окне, но `RecyclerView` предоставляет дополнительные возможности макета и таким образом оптимизируется для отображения больших коллекций. Если вы знакомы с `ListView`, существует несколько важных различий между `ListView` и `RecyclerView`:

-   `RecyclerView` немного более сложна для использования: необходимо написать дополнительный код для использования `RecyclerView` по сравнению с `ListView`.

-   `RecyclerView` не поддерживает предопределенные адаптера; необходимо реализовать код адаптера, который обращается к источнику данных. Тем не менее Android включает несколько стандартных адаптеров, которые работают с `ListView` и `GridView`.

-   `RecyclerView` не предлагает щелчком элемента событие, когда пользователь касается элемента; Вместо этого щелкните элемент события обрабатываются вспомогательные классы. Напротив `ListView` предлагает события щелчка элемента.

-   `RecyclerView` повторное использование представлений и применяя шаблон представление владельца, который избавляет от ненужных макета операций поиска ресурсов, повышает производительность. Использование шаблона представления владельца является необязательным в `ListView`.

-   `RecyclerView` основан на модульную архитектуру, что упрощает процесс настройки. Например можно подключить в политике другой макет без значительно изменить код в приложение.
    Напротив `ListView` является относительно монолитных в структуре.

-   `RecyclerView` включает встроенные анимационные ролики для элемента, добавления и удаления. `ListView` анимации требуют некоторых дополнительных усилий со стороны разработчика приложения.


### <a name="sections"></a>Разделы

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[RecyclerView компоненты и функции](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

В этом разделе объясняется как `Adapter`, `LayoutManager`, и `ViewHolder` взаимодействуют между собой как вспомогательные классы для поддержки `RecyclerView`.
Он представлен общий обзор каждого из этих вспомогательных классов и объясняется, как их использовать в приложении.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Простой пример RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Материал данной статьи основан на информации из [RecyclerView компоненты и функции](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) , предоставляя реальные примеры того, как различные `RecyclerView` элементы реализуются как создать приложение просмотра фотографий реального мира.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Расширим пример RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

В этом разделе добавляет дополнительный код для примера приложения, представленные в [базовый пример RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) демонстрирует, как обрабатывать события щелчка элемента и обновлять `RecyclerView` при изменении базовых данных источника.


### <a name="summary"></a>Сводка

Это руководство содержит общие Android `RecyclerView` мини-приложения; оно описывает способы добавления `RecyclerView` поддержки библиотеки в проекты Xamarin.Android, как `RecyclerView` перезапускается представления, он применяет шаблон представление владелец для повышения эффективности, а также различных вспомогательные классы, составляющие `RecyclerView` сотрудничают, чтобы отобразить коллекции. Его предоставленный пример кода демонстрирует, как `RecyclerView` интегрирован в приложение, оно было рассмотрено, как настроить `RecyclerView`элемента политики макета, подключив в диспетчеры другой макет и он было описано, как обрабатывать элемент события click и уведомить `RecyclerView`изменений источника данных.

Дополнительные сведения о `RecyclerView`, см. в разделе [ссылки на класс RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).


## <a name="related-links"></a>Связанные ссылки

- [RecyclerViewer (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Общие сведения о без описания операций](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
