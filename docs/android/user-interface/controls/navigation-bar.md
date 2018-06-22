---
title: Панель переходов
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/01/2017
ms.openlocfilehash: 476e73906e4fbe01847740f90a8da701768b29db
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762816"
---
# <a name="navigation-bar"></a>Панель переходов

Android 4 появился новый компонент интерфейса пользователя системы, называемый *панель навигации*, который предоставляет элементы управления навигацией на устройствах, которые не содержат кнопок для **Главная**, **назад** , и **меню**.
На следующем рисунке показан на панели навигации, от простых хранилища устройства:

 [![Пример панели навигации Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

Доступны несколько новых флагов, управления видимостью панели навигации и ее элементов управления, а также видимость панель системы, которая была введена в Android 3. Флаги определяются в `Android.View.View` класса и перечислены ниже:

-   `SystemUiFlagVisible` &ndash; Делает видимыми на панели навигации. 
-   `SystemUiFlagLowProfile` &ndash; Dims элементов управления в панели навигации. 
-   `SystemUiFlagHideNavigation` &ndash; Скрывает панель навигации. 


Эти флаги могут применяться к любому представлению в иерархии представления, задав `SystemUiVisibility` свойство. Если это свойство установлено несколько представлений, система объединяет их с помощью операции OR и применяет их при условии, что окно, в котором устанавливаются флаги сохраняется фокусировки. При удалении представления, будут также удалены все флажки будут установлены.

В следующем примере показано простое приложение, щелкнув любую из кнопок изменения где `SystemUiVisibility`:

 [![Демонстрация Visible половинной и скрытые SystemUiVisibility снимки экрана](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

Код для изменения `SystemUiVisibility` присваивает свойству `TextView` из каждой кнопки нажмите кнопку обработчика событий, как показано ниже:

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

Кроме того `SystemUiVisibility` изменить вызывает `SystemUiVisibilityChange` событий. Так же, как параметр `SystemUiVisibility` свойство, обработчик `SystemUiVisibilityChange` событий может регистрироваться для любого представления в иерархии. Например, приведенный ниже код использует `TextView` экземпляр для регистрации события:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>Связанные ссылки

- [SystemUIVisibilityDemo (пример)](https://developer.xamarin.com/samples/monodroid/SystemUIVisibilityDemo/)
- [Знакомство с приложением Сандвичевы мороженого](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 платформы](http://developer.android.com/sdk/android-4.0.html)
