---
title: Замена панели действий
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: 9e9fa1e2651661670f89baac7fcd438b3d14bfb3
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108227"
---
# <a name="replacing-the-action-bar"></a>Замена панели действий

## <a name="overview"></a>Обзор

Одно из наиболее распространенных применений `Toolbar` — замена панели действий по умолчанию пользовательский `Toolbar` (при создании нового проекта Android, он использует панели действий по умолчанию). Так как `Toolbar` предоставляет возможность добавления фирменной символикой логотипы, заголовки, элементы меню, кнопки навигации и даже пользовательские представления в приложение панели раздел действия пользовательского интерфейса, он предлагает значительные обновления на панели действий по умолчанию.

Для замены панели действий по умолчанию приложения с `Toolbar`: 

1.  Создайте новый пользовательскую тему и изменить свойства приложения, таким образом, чтобы она использует эту новую тему. 

2.  Отключить `windowActionBar` атрибутов в пользовательской темы и включить `windowNoTitle` атрибута.

3.  Определить макет для `Toolbar`.

4.  Включить `Toolbar` макета в действии **Main.axml** файл макета. 

5.  Добавьте код в действие `OnCreate` метод необходимо найти `Toolbar` и вызвать `SetActionBar` установка `ToolBar` как на панели действий.

В следующих разделах объясняется этот процесс подробно. Создается простое приложение и заменяется его панели действий с настраиваемый `Toolbar`. 



## <a name="start-an-app-project"></a>Запуск проекта приложения

Создайте новый проект Android с именем **ToolbarFun** (см. в разделе [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md) Дополнительные сведения о создании нового проекта Android). После создания этого проекта равным уровнях Android API целевую и минимальную **Android 5.0 (API уровня 21 - без описания операций)** или более поздней версии. Дополнительные сведения о задании версия Android уровней см. в разделе [уровнях API Android понимание](~/android/app-fundamentals/android-api-levels.md). При построения и запуска приложения, как показано на следующем снимке экрана отображается на панели действий по умолчанию:

[![Снимок экрана панели действий по умолчанию](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)



## <a name="create-a-custom-theme"></a>Создайте пользовательскую тему

Откройте **ресурсы и значения, которые** directory и создать новый файл с именем **styles.xml**. Замените его содержимое следующим кодом XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="@android:style/Theme.Material.Light.DarkActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowActionBar">false</item>
    <item name="android:colorPrimary">#5A8622</item>
  </style>
</resources>
```

Этот XML-документ определяет новые пользовательские темы вызывается **MyTheme** , основанный на **Theme.Material.Light.DarkActionBar** темы в без описания операций. `windowNoTitle` Атрибуту присваивается `true` Чтобы скрыть панель заголовка: 

```xml
<item name="android:windowNoTitle">true</item>
```

Чтобы отобразить пользовательскую панель инструментов по умолчанию `ActionBar` необходимо отключить: 

```xml
<item name="android:windowActionBar">false</item>
```

Olive-green `colorPrimary` параметр используется для цвет фона панели инструментов: 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>Применить пользовательские темы

Изменить **Properties/AndroidManifest.xml** и добавьте следующие `android:theme` атрибут `<application>` элемент таким образом, приложение использует `MyTheme` пользовательские темы: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

Дополнительные сведения о применении пользовательской темы для приложения, см. в разделе [пользовательские темы, с помощью](~/android/user-interface/material-theme.md#customtheme). 



## <a name="define-a-toolbar-layout"></a>Макет панели инструментов

В **ресурсы/макета** directory, создайте новый файл с именем **toolbar.xml**. Замените его содержимое следующим кодом XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?android:attr/actionBarSize"
    android:background="?android:attr/colorPrimary"
    android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"/>
```

Этот XML-код определяет пользовательский `Toolbar` , заменяет панели действий по умолчанию. Минимальная высота `Toolbar` устанавливается размер панели действий, которые он заменяет: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

Цвет фона `Toolbar` присваивается olive-green цветом, определенным ранее в **styles.xml**:

```csharp
android:background="?android:attr/colorPrimary"
```

Начиная с без описания операций, `android:theme` атрибут может использоваться для определения стиля отдельного представления. `ThemeOverlay.Material` Темы, представленные в Lollipop делают возможным наложения по умолчанию `Theme.Material` перезапись соответствующие атрибуты, чтобы сделать их светлой или темной темы. В этом примере `Toolbar` использует темной таким образом, его содержимое светлым: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Этот параметр используется, чтобы элементы меню Сравните с темным цветом фона.



## <a name="include-the-toolbar-layout"></a>Включить макет панели инструментов

Измените файл макета **Resources/layout/Main.axml** и замените его содержимое следующим кодом XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
</RelativeLayout>
```

Этот макет включает `Toolbar` определенные в **toolbar.xml** и использует `RelativeLayout` позволяет указать, что `Toolbar` следует поместить в самом верху пользовательского интерфейса (над кнопкой). 



## <a name="find-and-activate-the-toolbar"></a>Поиск и активировать панель инструментов

Изменить **MainActivity.cs** и добавьте следующий оператор using:

```csharp
using Android.Views;
```

Кроме того, добавьте следующие строки кода в конец `OnCreate` метод:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Этот код находит `Toolbar` и вызывает метод `SetActionBar` таким образом, чтобы `Toolbar` займет на характеристиках панели действий по умолчанию. Заголовок панели меняется на **Мои инструментов**. Как показано в этом примере кода `ToolBar` можно непосредственно ссылаться как на панель действий. Скомпилируйте и запустите это приложение &ndash; Настраиваемая `Toolbar` вместо панели действий по умолчанию отображается: 

[![Снимок экрана пользовательскую панель инструментов с зеленый цветовой схемы](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Обратите внимание, что `Toolbar` реализуется независимо от `Theme.Material.Light.DarkActionBar` тему, применяемую к оставшейся части приложения. 

При возникновении исключения во время выполнения приложения, см. в разделе [Устранение неполадок](#troubleshooting) разделе ниже.

 
## <a name="add-menu-items"></a>Добавление элементов меню 

В этом разделе добавляются меню `Toolbar`. Верхней правой области `ToolBar` зарезервирован для пунктов меню &ndash; каждым пунктом меню (также называется *элемент действия*) можно выполнять различные действия в текущем действии или выполнять действия от имени всего приложения. 

Чтобы добавить меню `Toolbar`: 

1.  Добавление значков (при необходимости) `mipmap-` папки проекта приложения. Компания Google предоставляет набор значков меню бесплатно на [материала значки](https://design.google.com/icons/) страницы. 

2.  Определения содержимого из пунктов меню, добавив новый файл ресурсов меню в разделе **ресурсы/меню**. 

3.  Реализуйте `OnCreateOptionsMenu` метод действия &ndash; этот метод увеличивает пунктов меню. 

4.  Реализуйте `OnOptionsItemSelected` метод действия &ndash; этот метод выполняет действие, при нажатии пункта меню. 

В следующих разделах демонстрируются этот процесс подробно, добавив **изменить** и **Сохранить** пунктов меню, чтобы пользовательское `Toolbar`. 



### <a name="install-menu-icons"></a>Установка значков

Продолжая `ToolbarFun` приложении-примере добавление значков в проект приложения. Скачайте [значки панели инструментов](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true), распакуйте и скопируйте содержимое извлеченный *mipmap -* папки в проект *mipmap -* папках **ToolbarFun / Ресурсы** и включите каждый файл, добавленный значок в проекте.


### <a name="define-a-menu-resource"></a>Определение ресурсов меню

Создайте новый **меню** вложенную **ресурсы**. В **меню** подкаталог, создайте новый файл ресурсов меню с именем **top_menus.xml** и замените его содержимое следующим кодом XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       android:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       android:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       android:showAsAction="never"
       android:title="Preferences" />
</menu>
```

Этот XML-код создает три элемента меню:

-   **Изменить** пункт меню, который использует `ic_action_content_create.png` значок (Карандаш). 

-   Объект **Сохранить** пункт меню, который использует `ic_action_content_save.png` значок (дискету). 

-   Объект **предпочтения** пункт меню, который не имеет значка.

`showAsAction` Атрибуты **изменить** и **Сохранить** присваивается пунктов меню `ifRoom` &ndash; этот параметр задан, то эти элементы меню отображались в `Toolbar` при наличии достаточно места для отображения. **Предпочтения** набора элементов меню `showAsAction` для `never` &ndash; в результате **предпочтения** меню будет отображаться в *переполнения* меню (три вертикальные точки). 


### <a name="implement-oncreateoptionsmenu"></a>Реализовать OnCreateOptionsMenu

Добавьте следующий метод в **MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android вызывает `OnCreateOptionsMenu` метод таким образом, приложение может указать ресурс меню для действия. В этом методе **top_menus.xml** ресурсов увеличивается в переданный `menu`. Этот код вызывает новый **изменить**, **Сохранить**, и **предпочтения** пунктов меню в `Toolbar`. 



### <a name="implement-onoptionsitemselected"></a>Реализовать OnOptionsItemSelected

Добавьте следующий метод в **MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

При касании пункта меню, вызывает Android `OnOptionsItemSelected` и передает в пункте меню, которая была выбрана. В этом примере реализация просто отображает всплывающее уведомление, чтобы указать, какой элемент меню был касание. 

Сборка и запуск `ToolbarFun` новые элементы меню на панели инструментов. `Toolbar` Теперь отображает три значка меню, как показано на следующем снимке экрана: 

[![Диаграмма иллюстрирующая расположение редактирования, сохранения и элементов меню переполнения](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Когда пользователь касается **изменить** отображается пункт меню, всплывающее уведомление, чтобы указать, что `OnOptionsItemSelected` был вызван метод: 

[![Снимок экрана из всплывающего отображается при нажатии элемента редактирования](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Когда пользователь нажимает меню переполнения, **предпочтения** отображения пункта меню. Как правило, менее распространенных действий должны размещаться в меню переполнения &ndash; в этом примере используется меню переполнения для **предпочтения** , так как он используется не так часто, как **изменить** и  **Сохранить**: 

[![Снимок экрана для настройки меню, отображаемый в меню переполнения](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Дополнительные сведения о меню Android, см. в разделе разработчика Android [меню](https://developer.android.com/guide/topics/ui/menus.html) раздела. 
 

## <a name="troubleshooting"></a>Устранение неполадок

Следующие советы помогут для отладки проблем, возникающих при замене панели действий с панели инструментов.

### <a name="activity-already-has-an-action-bar"></a>Действие уже имеет панель действий

Если приложение не настроено для использования пользовательской темы, как описано в [применить пользовательскую тему](#apply-the-custom-theme), при запуске приложения может возникнуть следующее исключение:

![Ошибка, которая может возникнуть при пользовательской темы не используется](replacing-the-action-bar-images/03-theme-not-defined.png)

Кроме того, сообщение об ошибке такие как следующие может быть получена: _Java.Lang.IllegalStateException: это действие уже предоставляемые декоративных окно Панель действий._ 

Чтобы исправить эту ошибку, убедитесь, что `android:theme` атрибут добавляется пользовательская тема `<application>` (в **Properties/AndroidManifest.xml**) как описано выше в [применить пользовательскую тему](#apply-the-custom-theme). Кроме того, эта ошибка может быть вызвана, если `Toolbar` макета или пользовательские темы не настроен должным образом.


## <a name="related-links"></a>Связанные ссылки

- [Панель инструментов без описания операций (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Панель инструментов совместимости приложений (пример)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
