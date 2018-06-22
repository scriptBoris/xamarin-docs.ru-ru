---
title: Всплывающее меню
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/18/2017
ms.openlocfilehash: e7fad84133ca712c531ab0d12a67db78103c7cdd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763154"
---
# <a name="popup-menu"></a>Всплывающее меню

`PopupMenu` Класс добавляет поддержку отображение всплывающих меню, прикрепленных к конкретному представлению. На следующем рисунке показано всплывающее меню, откроется второй элемент выделен так же, как он установлен с помощью кнопки:

 [![Пример о PopopMenu с тремя трех элементов](popup-menu-images/20-popupmenu.png)](popup-menu-images/20-popupmenu.png#lightbox)

Android 4 добавлено несколько новых возможностей в `PopupMenu` сделать более удобным для работы, а именно:

-   **Inflate** &ndash; увеличению метод теперь доступен в классе PopupMenu напрямую.
-   **DismissEvent** &ndash; PopupMenu класс теперь имеет DismissEvent.

Давайте рассмотрим эти усовершенствования. В этом примере у нас есть одно действие, которое содержит кнопку. Когда пользователь нажимает кнопку, отображается всплывающего меню, как показано ниже:

 [![Пример приложения в эмуляторе с кнопками и 3 элемента всплывающего меню](popup-menu-images/06-popupmenu.png)](popup-menu-images/06-popupmenu.png#lightbox)


## <a name="creating-a-popup-menu"></a>Создание всплывающего меню

Когда создается экземпляр `PopupMenu`, нам нужно передать ссылку на его конструктор `Context`, а также представления, к которому присоединяется меню. В этом случае мы создадим `PopupMenu` в обработчик события нажатия кнопки для наших кнопки, которая называется `showPopupMenu`.
Эта кнопка также имеет представление, к которому будет присоединить `PopupMenu`, как показано в следующем коде:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
}
```

В Android 3, код, на которую требуется увеличить меню из ресурса XML необходимо сначала получить ссылку на `MenuInflator`, а затем вызвать его `Inflate` метод с Идентификатором ресурса XML, содержащийся в меню и меню экземпляра, на которую увеличится в. Такой подход по-прежнему работает в Android 4 и более поздней версии в качестве кода ниже показано:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.MenuInflater.Inflate (Resource.Menu.popup_menu, menu.Menu);
};
```

Начиная с Android 4 тем не менее, теперь можно вызвать `Inflate` непосредственно на экземпляре `PopupMenu`. Это делает код более компактные, как показано ниже:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

В приведенном выше коде после дало бы ошибку в меню мы просто вызвать `menu.Show` для его отображения на экране.


## <a name="handling-menu-events"></a>Обработка событий меню

Когда пользователь выбирает пункт меню `MenuItemClick` возникает событие и меню будет закрыт. Коснувшись в любом месте за пределами меню будет просто закрыть его. В любом случае, начиная с Android 4, при закрытии меню его `DismissEvent` будет вызываться. Следующий код добавляет обработчики событий для обоих `MenuItemClick` и `DismissEvent` событий:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);

    menu.MenuItemClick += (s1, arg1) => {
        Console.WriteLine ("{0} selected", arg1.Item.TitleFormatted);
    };

    menu.DismissEvent += (s2, arg2) => {
        Console.WriteLine ("menu dismissed");
    };
            menu.Show ();
};
```



## <a name="related-links"></a>Связанные ссылки

- [PopupMenuDemo (пример)](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)
- [Знакомство с приложением Сандвичевы мороженого](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 платформы](http://developer.android.com/sdk/android-4.0.html)
