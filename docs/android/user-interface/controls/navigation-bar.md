---
title: Панель переходов
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: ce80fab39c814204631c5cc408c3f0ee99a329e6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110561"
---
# <a name="navigation-bar"></a>Панель переходов

Android 4 появилась новая функция интерфейса пользователя системы, называемая *панель навигации*, который предоставляет элементы управления навигацией на устройствах, не включающие кнопок для **Главная**, **обратно** , и **меню**.
На следующем рисунке показан на панели навигации с простых Nexus устройства:

 [![Пример того, в панели навигации в Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

Доступны несколько новые флаги, управляющие видимостью панели навигации и ее элементов управления, а также видимости полосы системы, которая была введена в Android 3. Флаги определяются в `Android.View.View` класса и перечислены ниже:

-   `SystemUiFlagVisible` &ndash; Делает видимыми на панели навигации. 
-   `SystemUiFlagLowProfile` &ndash; Dims элементов управления на панели навигации. 
-   `SystemUiFlagHideNavigation` &ndash; Скрытие панели навигации. 


Эти флаги могут применяться для любого представления в иерархии представлений, задав `SystemUiVisibility` свойство. Если это свойство установлено несколько представлений, система объединяет их с помощью операции OR и применяет их до тех пор, пока окно, в котором устанавливаются флаги сохраняет фокус. При удалении представления, также удаляются все флаги, которые будут установлены.

В следующем примере показано простое приложение, в которых щелкнув любую кнопку меняет `SystemUiVisibility`:

 [![Демонстрация Visible половинной и скрытые SystemUiVisibility снимки экрана](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

Код для изменения `SystemUiVisibility` присваивает свойству `TextView` из каждой кнопки щелкните обработчик событий, как показано ниже:

```csharp
var tv = FindViewById<TextView> (Resource.Id.systemUiFlagTextView);
var lowProfileButton = FindViewById<Button>(Resource.Id.lowProfileButton);
var hideNavButton = FindViewById<Button> (Resource.Id.hideNavigation);
var visibleButton = FindViewById<Button> (Resource.Id.visibleButton);
           
lowProfileButton.Click += delegate {
    tv.SystemUiVisibility =
        (StatusBarVisibility)View.SystemUiFlagLowProfile;
};
           
hideNavButton.Click += delegate {
    tv.SystemUiVisibility =
       (StatusBarVisibility)View.SystemUiFlagHideNavigation;        
};
           
visibleButton.Click += delegate {
    tv.SystemUiVisibility = (StatusBarVisibility)View.SystemUiFlagVisible;
}
```

Кроме того `SystemUiVisibility` изменить вызывает `SystemUiVisibilityChange` событий. Так же, как параметр `SystemUiVisibility` свойство, обработчик `SystemUiVisibilityChange` событий могут быть зарегистрированы для любого представления в иерархии. Например, приведенный ниже код использует `TextView` экземпляр для регистрации для события:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>Связанные ссылки

- [SystemUIVisibilityDemo (пример)](https://developer.xamarin.com/samples/monodroid/SystemUIVisibilityDemo/)
- [Знакомство с Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Платформа Android 4.0](http://developer.android.com/sdk/android-4.0.html)
