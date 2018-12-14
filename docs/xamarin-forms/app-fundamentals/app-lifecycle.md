---
title: Жизненный цикл приложения Xamarin.Forms
description: В этой статье описывается обработка жизненного цикла приложения, включая методы жизненного цикла, события навигации по страницам и события модальной навигации.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 5bdce8d1752b3d7273ffec233b120266909999c4
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675124"
---
# <a name="xamarinforms-app-lifecycle"></a>Жизненный цикл приложения Xamarin.Forms

Базовый класс [`Application`](xref:Xamarin.Forms.Application) предоставляет следующие возможности:

* [методы жизненного цикла](#Lifecycle_Methods) `OnStart`, `OnSleep` и `OnResume`;
* [события навигации по страницам](#page) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) и [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing);
* [события модальной навигации](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping` и `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Методы жизненного цикла

Класс [`Application`](xref:Xamarin.Forms.Application) содержит три виртуальных метода, которые можно переопределить для обработки методов жизненного цикла:

* **OnStart** — вызывается при запуске приложения;

* **OnSleep** — вызывается каждый раз, когда приложение переводится в фоновый режим;

* **OnResume** — вызывается каждый раз, когда приложение выводится из фонового режима и его работа возобновляется.

Обратите внимание на то, что метода, отвечающего за завершение работы приложения, *нет*.
В обычных условиях (то есть не в случае сбоя) завершение работы приложения происходит из состояния *OnSleep* без дополнительных уведомлений в коде.

Чтобы пронаблюдать за вызовом этих методов, реализуйте вызов `WriteLine` в каждом из них (как показано ниже) и протестируйте приложение на каждой платформе.

```csharp
protected override void OnStart()
{
    Debug.WriteLine ("OnStart");
}
protected override void OnSleep()
{
    Debug.WriteLine ("OnSleep");
}
protected override void OnResume()
{
    Debug.WriteLine ("OnResume");
}
```

При обновлении *более старых* приложений Xamarin.Forms (например, созданных с помощью Xamarin.Forms 1.3 или более ранней версии) убедитесь в том, что основное действие Android включает `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` в атрибуте `[Activity()]`. Если этот элемент отсутствует, метод `OnStart` будет вызываться при каждом повороте экрана, а не только при первом запуске приложения. В текущие шаблоны приложений Xamarin.Forms данный атрибут включен по умолчанию.

<a name="page" />

## <a name="page-navigation-events"></a>События навигации по страницам

В классе [`Application`](xref:Xamarin.Forms.Application) есть два события, которые уведомляют о появлении страниц на экране и их исчезновении с экрана:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) — возникает, когда страница скоро появится на экране;
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) — возникает, когда страница скоро пропадет с экрана.

Эти события можно использовать, когда нужно отслеживать появление страниц на экране.

> [!NOTE]
> События [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) и [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) вызываются из базового класса [`Page`](xref:Xamarin.Forms.Page) сразу после событий [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) и [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) соответственно.

<a name="modal" />

## <a name="modal-navigation-events"></a>События модальной навигации

В классе [`Application`](xref:Xamarin.Forms.Application) есть четыре события (каждый с собственными аргументами), которые позволяют реагировать на открытие и закрытие модальных страниц:

* **ModalPushing** - `ModalPushingEventArgs`;
* **ModalPushed** - `ModalPushedEventArgs`;
* **ModalPopping** — класс `ModalPoppingEventArgs` содержит свойство `Cancel`; когда свойству `Cancel` присваивается значение `true`, модальное всплывающее окно отменяется;
* **ModalPopped** - `ModalPoppedEventArgs`.

> [!NOTE]
> Чтобы реализовать методы жизненного цикла приложения и события модальной навигации, все методы создания приложения, использовавшиеся до появления класса `Application` в Xamarin.Forms (то есть для приложений, написанных на основе версии 1.2 или более ранней и использующих статический метод `GetMainPage`), были обновлены для создания объекта `Application` по умолчанию, являющегося родительским для `MainPage`.
>
> Устаревшие приложения Xamarin.Forms необходимо обновить так, чтобы в них использовался подкласс `Application`, как описано на странице [Класс Application](~/xamarin-forms/app-fundamentals/application-class.md).
