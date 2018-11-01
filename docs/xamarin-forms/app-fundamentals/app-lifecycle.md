---
title: Жизненный цикл приложения Xamarin.Forms
description: В этой статье объясняется, как реагировать на жизненный цикл приложения, включая методы жизненного цикла, события навигации страницы и события модальной навигации.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 5bdce8d1752b3d7273ffec233b120266909999c4
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675124"
---
# <a name="xamarinforms-app-lifecycle"></a>Жизненный цикл приложения Xamarin.Forms

[ `Application` ](xref:Xamarin.Forms.Application) Базовый класс предоставляет следующие возможности:

* [Методы жизненного цикла](#Lifecycle_Methods) `OnStart`, `OnSleep`, и `OnResume`.
* [События навигации страницы](#page) [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing), [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing).
* [Модальная Навигация события](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping`, и `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Методы жизненного цикла

[ `Application` ](xref:Xamarin.Forms.Application) Класс содержит три виртуальные методы, которые могут быть переопределены для обработки методов жизненного цикла:

* **OnStart** -вызывается при запуске приложения.

* **OnSleep** -вызывается каждый раз при активации приложения в фоновом режиме.

* **OnResume** -вызывается при возобновлении приложения после, отправляемые в фоновом режиме.

Следует отметить, что *не* метод для завершения приложения.
В обычных условиях (т. е. не сбоя) произойдет завершение работы приложения из *OnSleep* состояния без дополнительных уведомления в код.

Чтобы посмотреть, когда эти методы вызываются, реализовать `WriteLine` вызов в каждом (как показано ниже) и тестирование на каждой платформе.

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

При обновлении *старые* приложений Xamarin.Forms (например) необходимо создать с помощью Xamarin.Forms 1.3 или более ранних версий), убедитесь, что Android основное действие включает `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` в `[Activity()]` атрибута. Если они отсутствуют будет контролироваться `OnStart` на поворот, а также при первом запуске приложения вызывается метод. Этот атрибут автоматически включается в текущее шаблоны приложений Xamarin.Forms.

<a name="page" />

## <a name="page-navigation-events"></a>Навигация по страницам события

Существуют два события на [ `Application` ](xref:Xamarin.Forms.Application) класса, уведомления о страницах появляются и исчезают:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) — вызывается, когда страница будет отображаться на экране.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) — вызывается, когда страница собирается пропадает с экрана.

Эти события можно использовать в сценариях где требуется отслеживать страницы, так как они появляются на экране.

> [!NOTE]
> [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing) И [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing) событий из [ `Page` ](xref:Xamarin.Forms.Page) базового класса сразу после [ `Page.Appearing` ](xref:Xamarin.Forms.Page.Appearing) и [ `Page.Disappearing` ](xref:Xamarin.Forms.Page.Disappearing) события, соответственно.

<a name="modal" />

## <a name="modal-navigation-events"></a>Модальная Навигация события

Существует четыре события на [ `Application` ](xref:Xamarin.Forms.Application) класс, каждый из которых свои собственные аргументы события, которые позволяют реагировать на них модальные страницы показано, а закрыто:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - `ModalPoppingEventArgs` класс содержит `Cancel` свойство. Когда `Cancel` присваивается `true` модальное pop отменяется.
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> Чтобы реализовать методы жизненного цикла приложения и события модальной навигации, все предварительные`Application` способы создания приложения Xamarin.Forms (т. е. на приложения, написанные в версии 1.2 или более ранней версии, которые используют статическую `GetMainPage` метод) были обновлены, чтобы создать по умолчанию `Application` которого задано значение в качестве родительского для `MainPage`.
>
> Приложения Xamarin.Forms, к поведению предыдущих версий необходимо обновить, чтобы `Application` подкласс, как описано в разделе [класс приложения](~/xamarin-forms/app-fundamentals/application-class.md) страницы.
