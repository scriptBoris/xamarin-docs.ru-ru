---
title: Добавление второй панели инструментов
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: fc95c05c1945464cd9cac8565d8a11ff1b4c7e1d
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527291"
---
# <a name="adding-a-second-toolbar"></a>Добавление второй панели инструментов


## <a name="overview"></a>Обзор 

`Toolbar` Можно сделать более чем заменить на панели действий &ndash; он может использоваться несколько раз в рамках действия, его можно настроить для размещения в любом месте на экране и его можно настроить охватывать только частичное ширину экрана. В приведенных ниже примерах показаны способы создания второй `Toolbar` и поместите его в нижней части экрана. Это `Toolbar` реализует **копирования**, **Вырезать**, и **вставить** пунктов меню. 


## <a name="define-the-second-toolbar"></a>Определите второй панели инструментов 

Измените файл макета **Main.axml** и замените его содержимое с следующий код XML:

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

Этот XML-код добавляет второй `Toolbar` в нижнюю часть экрана с пустым `ImageView` заполнения в середине экрана. Высота этой `Toolbar` присваивается высоту панели действий: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

Цвет фона объекта `Toolbar` присваивается контрастный цвет, которые будут определены далее:

```xml
android:background="?android:attr/colorAccent
```

Обратите внимание, что это `Toolbar` основан на другую тему (**ThemeOverlay.Material.Dark.ActionBar**) от используемого по `Toolbar` в [замена панели действий](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash;его не привязанным к декоративных окно действия или тему, используемую в первый `Toolbar`.

Изменить **Resources/values/styles.xml** и добавьте следующий цвет в определение стиля: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

Это дает темно-оранжевый цвет нижней панели инструментов. Построение и выполнение приложения отображает пустое второй панели инструментов в нижней части экрана: 

[![Снимок экрана приложения с желтой второй панели инструментов в нижней части экрана](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)


 
## <a name="add-edit-menu-items"></a>Добавление элементов меню Edit 

В этом разделе описывается добавление элементов меню редактирования в нижней `Toolbar`. 

Добавление элементов меню в базу данных-получатель `Toolbar`: 

1.  Добавление значков для `mipmap-` папки проекта приложения (при необходимости).

2.  Определения содержимого из пунктов меню, добавив файл ресурсов меню для **ресурсы/меню**. 

3.  В действии `OnCreate` метод, найти `Toolbar` (путем вызова `FindViewById`) и расширение `Toolbar`элемента меню.

4.  Реализуйте обработчик щелчка в `OnCreate` для новых пунктов меню. 

В следующих разделах демонстрируются этот процесс подробно: **Вырезать**, **копирования**, и **вставить** добавляются в нижнюю `Toolbar`. 



### <a name="define-the-edit-menu-resource"></a>Определите ресурс меню Edit

В **ресурсы/меню** подкаталог, создайте новый файл XML с именем **edit_menus.xml** и замените его содержимое следующим кодом XML:

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

Этот XML-код создает **Вырезать**, **копирования**, и **вставить** пунктов меню (с помощью значков, которые были добавлены `mipmap-` папок в [замена панели действий ](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).



### <a name="inflate-the-menus"></a>Расширение меню

В конце `OnCreate` метод в **MainActivity.cs**, добавьте следующие строки кода: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Этот код находит `edit_toolbar` представление, определенное в **Main.axml**, задает заголовок **редактирование**и увеличивает его элементами меню (определенные в **edit_menus.xml**). Он определяет меню отображается всплывающее уведомление, чтобы указать, какой значок редактирования был касание обработчик нажатия. 

Выполните сборку и запуск приложения. При запуске приложения, текста и значков, добавленных выше будет отображаться следующим образом: 

[![Схема нижней панели инструментов со значками вырезания, копирования и вставки](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

При касании **Вырезать** значок меню вызывает следующее всплывающее уведомление для отображения: 

[![Снимок экрана из всплывающего означает, что значок меню вырезания был касание](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

При касании пунктов меню в любом инструментов отображает результирующий всплывающие уведомления: 

[![Снимки экрана из всплывающие уведомления для сохранение, копирование и вставьте элементы меню касание](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)



## <a name="the-up-button"></a>Со стрелкой вверх 

Большинство приложений Android используют по **обратно** кнопки для навигации приложения; нажатие **обратно** кнопки перехода пользователя к предыдущему экрану.
Тем не менее, может также потребоваться предоставить **вверх** кнопка, которая позволяет пользователям перемещаться по «вверх», главное окно приложения. Когда пользователь выбирает **вверх** кнопка перемещается до более высокого уровня в иерархии приложения &ndash; то есть в приложении появится всплывающее обратно пользователю несколько действий в стек переходов назад, а не извлечения обратно в ранее выбранной Действие. 

Чтобы включить **вверх** кнопку во второй активности, использующий `Toolbar` его панели действий, вызовите `SetDisplayHomeAsUpEnabled` и `SetHomeButtonEnabled` методы в второе действие `OnCreate` метод:

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

[Поддержки инструментов версии 7](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/) образце кода показано **вверх** кнопка. В этом примере (с использованием библиотеки AppCompat, описанным далее) реализует это второе действие, которое использует панели инструментов **вверх** кнопки для иерархической навигации к предыдущим действием. В этом примере `DetailActivity` кнопка "Главная" позволяет **вверх** кнопки, сделав следующее `SupportActionBar` вызовов методов: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Когда пользователь переходит из `MainActivity` для `DetailActivity`, `DetailActivity` отображает **вверх** кнопки (указывающего стрелка влево), как показано на снимке экрана:

[![Снимок экрана пример левой стрелки вверх кнопки на панели инструментов](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

При касании, это **вверх** кнопка вызывает приложение, чтобы вернуться к `MainActivity`. В более сложные приложения с несколькими уровнями иерархии нажав эту кнопку, будет возвращать пользователя верхнего уровня в приложении, а не к предыдущему экрану. 



## <a name="related-links"></a>Связанные ссылки

- [Панель инструментов без описания операций (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Панель инструментов совместимости приложений (пример)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
