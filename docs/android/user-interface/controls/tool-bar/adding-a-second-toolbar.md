---
title: Добавление второй панели инструментов
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: f2255ab5ac7e153266093877a1c52bfc08927a09
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766641"
---
# <a name="adding-a-second-toolbar"></a>Добавление второй панели инструментов


## <a name="overview"></a>Обзор 

`Toolbar` Можно сделать более чем заменить панели действий &ndash; он может использоваться несколько раз в рамках действия, она может быть, настроенные для размещения в любом месте на экране, и его можно настроить для охвата частичного ширина экрана. В приведенных ниже примерах показано, как для создания второго `Toolbar` и поместите его в нижней части экрана. Это `Toolbar` реализует **копирования**, **Вырезать**, и **вставить** пунктов меню. 


## <a name="define-the-second-toolbar"></a>Определите второй панели инструментов 

Измените файл макета **Main.axml** и замените его содержимое следующим кодом XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/main_content"
        android:layout_below="@id/toolbar">
      <ImageView
          android:layout_width="fill_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <Toolbar
          android:id="@+id/edit_toolbar"
          android:minHeight="?android:attr/actionBarSize"
          android:background="?android:attr/colorAccent"
          android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
          android:layout_width="match_parent"
          android:layout_height="wrap_content" />
    </LinearLayout>
</RelativeLayout>
```

Этот XML-код добавляет второй `Toolbar` в нижней части экрана с пустым `ImageView` заполнение середины экрана. Высота объекта `Toolbar` задано значение высоты в панели действий: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

Фоновый цвет `Toolbar` равно контрастный цвет, который будет определен далее:

```xml
android:background="?android:attr/colorAccent
```

Обратите внимание, это `Toolbar` основан на различные цветовые (**ThemeOverlay.Material.Dark.ActionBar**) от используемого по `Toolbar` создан в [замена панели действий](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash;оно не привязано к декоративных окно действия или тему, используемую в первом `Toolbar`.

Изменить **Resources/values/styles.xml** и добавьте следующий цвет диакритических знаков в определение стиля: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

Это дает темной оранжевый цвет нижней панели инструментов. Построение и запуск приложения отображается пустая панель инструментов, вторая в нижней части экрана: 

[![Снимок экрана приложения с желтым второй панели инструментов в нижней части экрана](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)


 
## <a name="add-edit-menu-items"></a>Добавление элементов меню Правка 

В этом разделе описывается добавление элементов меню редактирования в нижней `Toolbar`. 

Чтобы добавить элементы на сервер-получатель `Toolbar`: 

1.  Добавление значков для `mipmap-` папки проекта приложения (при необходимости).

2.  Определите содержимое элементов меню, добавив файл ресурсов меню для **ресурсы или меню**. 

3.  В действии `OnCreate` метод, найти `Toolbar` (путем вызова `FindViewById`) и увеличению `Toolbar`элемента меню.

4.  Реализуйте обработчик щелчка в `OnCreate` нового пункта меню. 

В следующих разделах приводится подробное описание этого процесса: **Вырезать**, **копирования**, и **вставить** вниз добавляются пункты меню `Toolbar`. 



### <a name="define-the-edit-menu-resource"></a>Определить ресурс меню Правка

В **ресурсы или меню** подкаталог, создайте новый файл XML с именем **edit_menus.xml** и заменить его содержимое следующим кодом XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

Создает этот XML-документ **Вырезать**, **копирования**, и **вставить** пунктов меню (с помощью значков, которые были добавлены `mipmap-` папки в [замена панели действий ](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).



### <a name="inflate-the-menus"></a>Увеличению меню

В конце `OnCreate` метод в **MainActivity.cs**, добавьте следующие строки кода: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Этот код находит `edit_toolbar` представление, определенное в **Main.axml**, присваивает заголовку **редактирование**и увеличивает пунктов меню (определенные в **edit_menus.xml**). Он определяет меню щелкните обработчик, который отображает всплывающее уведомление для указания, было касании значок редактирования. 

Выполните сборку и запуск приложения. При запуске приложения, текста и значков, добавленных выше отображаются так, как показано ниже: 

[![Схема нижней панели инструментов с помощью вырезания, копирования и вставки значков](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

Коснувшись **Вырезать** меню значка вызывает следующие тост для отображения: 

[![Снимок экрана из всплывающих означает, что был касании значок меню перехода](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

Коснитесь пунктов меню или панели инструментов отображает результирующий всплывающие уведомления: 

[![Снимки экрана из всплывающие уведомления для сохранения копии и вставьте пунктов меню касание](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)



## <a name="the-up-button"></a>Кнопки вверх 

Большинство приложений Android полагаться на **обратно** кнопку навигации приложения, нажав клавишу **обратно** кнопки перехода пользователя к предыдущему экрану.
Однако можно также для предоставления **копирование** кнопки, которая позволяет пользователям перемещаться по «до «главное окно приложения. Когда пользователь выбирает **копирование** кнопку пользователь перемещается на более высоком уровне в иерархии приложения &ndash; то есть, приложение извлекает данные пользователя обратно несколько действий в стек, а не извлечение обратно в ранее выбранной Действие. 

Чтобы включить **копирование** кнопку в второе действие, которое использует `Toolbar` ее действие строки, вызовите `SetDisplayHomeAsUpEnabled` и `SetHomeButtonEnabled` методы в второго действия `OnCreate` метод:

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

[Инструментов v7 поддерживает](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/) пример кода демонстрирует **копирование** кнопка. В этом примере (с использованием библиотеки AppCompat, описанным ниже) реализует второе действие, которое использует панели инструментов **копирование** кнопки для иерархической навигации обратно в предыдущее действие. В этом примере `DetailActivity` кнопка «Главная» позволяет **копирование** кнопки, выполнив следующие `SupportActionBar` вызовы методов: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Когда пользователь переходит из `MainActivity` для `DetailActivity`, `DetailActivity` отображает **вверх** кнопки (указывающего стрелка влево), как показано на снимке экрана:

[![Снимок экрана примера левой стрелки вверх кнопки на панели инструментов](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

Коснувшись это **копирование** кнопка вызывает приложение, чтобы вернуться к `MainActivity`. В более сложных приложений с несколькими уровнями иерархии нажав эту кнопку, возвращает пользователя верхнего уровня в приложении, а не к предыдущему экрану. 



## <a name="related-links"></a>Связанные ссылки

- [Панель инструментов без описания операций (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Панель инструментов AppCompat (пример)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
