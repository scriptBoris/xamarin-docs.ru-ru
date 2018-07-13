---
title: Макет для планшета и классических приложений
description: В этой статье объясняется, как оптимизировать макетов приложений Xamarin.Forms для планшетных ПК, в отличие от телефонов.
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: b98d1fcf0917b9e25d774a92d56bf90bdd291978
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998640"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>Макет для планшета и классических приложений

Xamarin.Forms поддерживает все типы устройств, доступные на поддерживаемых платформах, поэтому помимо телефоны, приложения могут выполняться:

* iPad,
* Планшетов с Android
* Планшеты Windows и настольных компьютеров (под управлением Windows 10).

На этой странице кратко описаны:

* поддерживаемые [типы устройств](#Device_Types), и
* как [оптимизировать](#optimize) макеты для планшетов и телефонов.

<a name="Device_Types" />

## <a name="device-types"></a>Типы устройств

Большего размера экрана устройства, доступны для всех платформ, поддерживаемых Xamarin.Forms.

### <a name="ipads-ios"></a>устройства iPad (iOS)

Шаблон Xamarin.Forms автоматически включает поддержку iPad, настроив **Info.plist > устройств** присвоить **универсальной** (это означает, iPhone и iPad поддерживаются).

Для запуска приятной работы и убедиться, что разрешение во весь экран на всех устройствах, следует убедиться в том [экран iPad запуска](~/ios/app-fundamentals/images-icons/launch-screens.md) предоставляется (с помощью раскадровки). Это гарантирует, что приложение правильно отображается на устройствах iPad mini, iPad и iPad Pro.

До iOS 9 все приложения занимала во весь экран на устройстве, но некоторые устройства iPad теперь может выполнять [разделить экран многозадачность](~/ios/platform/multitasking.md).
Это означает, что приложения могут занять просто тонкий столбец боковой части экрана, 50% от ширины экрана или весь экран.

[![](tablet-images/ipad-sml.png "iPad разделение экрана образцу")](tablet-images/ipad.png#lightbox "iPad разделение экрана образцу")

Функциональные возможности разделенными экранами означает, что следует разрабатывать приложения для работы с как 320 пикселей в ширину или настолько, насколько 1366 пикселей в ширину.

### <a name="android-tablets"></a>Планшетов с Android

Android экосистемы имеет множество размеров поддерживаемых экрана, от небольших телефонов до больших планшетных ПК. Xamarin.Forms поддерживает все размеры экрана, но как с другими платформами можно настроить пользовательский интерфейс для устройств большего размера.

При поддержке множества различных разрешений экрана, можно предоставить ресурсы образов в машинном коде в разных размеров, чтобы оптимизировать взаимодействие с пользователем.
Просмотрите [ресурсы Android](~/android/app-fundamentals/resources-in-android/index.md) документации (и в частности [Создание ресурсов для изменения размеров экрана](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) Дополнительные сведения о том, как структура папок и имена файлов в приложении Android проект для включения оптимизированного образа ресурсы в приложении.

### <a name="windows-tablets-and-desktops"></a>Windows планшетных и настольных компьютерах

Для поддержки планшетных и настольных компьютерах с Windows, необходимо использовать [поддержки Windows UWP](~/xamarin-forms/platform/windows/installation/index.md), какие сборки универсальных приложений, работающих на Windows 10.

Приложения, работающие на Windows планшетных и настольных компьютерах можно изменять для произвольных размеров в дополнение к работе весь экран.

[![](tablet-images/splitscreen-sml.png "Пример экрана разделить Windows")](tablet-images/splitscreen.png#lightbox "Windows разделить пример экрана")


<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>Оптимизация для планшетных и настольных компьютеров

Вы можете указать пользовательский интерфейс Xamarin.Forms в зависимости от того, тип телефона или планшета/desktop устройство уже используется. Это означает, что вы можете оптимизировать работу пользователя для устройств большими экранами, такие как планшеты и настольных компьютеров.


### <a name="deviceidiom"></a>Device.Idiom

Можно использовать [ `Device` ](~/xamarin-forms/platform/device.md) класс для изменения поведения вашего приложения или в пользовательском интерфейсе. С помощью `Device.Idiom` перечисления, вы можете

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

Этот подход можно развернуть, вносить изменения в макеты отдельных страниц, или даже для отображения аналоговое страниц на больших экранах.

### <a name="leveraging-masterdetailpage"></a>Используя MasterDetailPage

[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) Идеально подходит для больших экранов, особенно на устройствах iPad, где он использует [ `UISplitViewController` ](https://developer.xamarin.com/api/type/UIKit.UISplitViewController/) для обеспечения работы машинном коде iOS.

Просмотрите [этой записи блога Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/) чтобы увидеть, как можно ее адаптировать пользовательский интерфейс телефоны использовать один макет и экраны больше можно использовать другой (с `MasterDetailPage`).



## <a name="related-links"></a>Связанные ссылки

- [Блог по Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)
- [Пример MyShoppe](https://github.com/jamesmontemagno/myshoppe)
