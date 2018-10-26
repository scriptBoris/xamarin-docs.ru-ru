---
title: Во всплывающем меню
description: Как добавить в контекстном меню, к которому привязана к конкретному представлению.
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: 1e74c8b7745936f6e9a8890fd26acafe2f2fb6d5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113889"
---
# <a name="popup-menu"></a>Во всплывающем меню

[PopupMenu](https://developer.xamarin.com/api/type/Android.Widget.PopupMenu/) (также называется _контекстное меню_) — это меню, к которому привязана к конкретному представлению. В следующем примере одно действие содержит кнопку. Когда пользователь нажимает кнопку, отображается три элемента всплывающего меню:

[![Пример приложения с помощью кнопки и три элемента всплывающего меню](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)


## <a name="creating-a-popup-menu"></a>Создание всплывающего меню

Первым шагом является создание файла ресурсов меню для меню и поместите его в **ресурсы/меню**. К примеру, следующий код XML-код для трех элементов меню, которое выводится в предыдущем снимке экрана, **Resources/menu/popup_menu.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/item1"
          android:title="item 1" />
    <item android:id="@+id/item1"
          android:title="item 2" />
    <item android:id="@+id/item1"
          android:title="item 3" />
</menu>
```

Создайте экземпляр `PopupMenu` и привязать его к виду. При создании экземпляра `PopupMenu`, вы передаете конструктору ссылку `Context` а также представления, к которому будет присоединен меню. Таким образом во всплывающем меню привязанный к этому представлению во время ее создания.

В следующем примере `PopupMenu` создается в обработчик событий click для кнопки (которая называется `showPopupMenu`). Эта кнопка также имеет представление, к которому `PopupMenu` привязан, как показано в следующем примере кода:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

Наконец, во всплывающем меню должен быть *увеличенную* меню ресурсом, который был создан ранее. В следующем примере вызов меню [Inflate](https://developer.xamarin.com/api/member/Android.Views.LayoutInflater.Inflate/p/System.Int32/Android.Views.ViewGroup/) добавляется метод и его [Показать](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Show%28%29/) вызывается метод, чтобы отобразить ее:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```


## <a name="handling-menu-events"></a>Обработка событий меню

Когда пользователь выбирает пункт меню, [MenuItemClick](https://developer.xamarin.com/api/event/Android.Widget.PopupMenu.MenuItemClick/) щелкните событие будет вызываться и меню будет закрыто. При касании в любом месте за пределами меню будет просто закройте его. В любом случае после закрытия меню его [DismissEvent](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Dismiss%28%29/) будет вызываться. Следующий код добавляет обработчики событий для обоих `MenuItemClick` и `DismissEvent` события:

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
