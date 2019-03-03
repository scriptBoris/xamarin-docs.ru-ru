---
title: Добавление AppCompat и Material Design
description: В этой статье объясняется, как преобразовать существующие приложения Xamarin.Forms Android использовать AppCompat и материальному дизайну.
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: c2eed44a7c684b91ceed4493a83ff3b4e1578b5f
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209319"
---
# <a name="adding-appcompat-and-material-design"></a>Добавление AppCompat и Material Design

_Выполните следующие действия для преобразования существующих приложений Xamarin.Forms Android использовать AppCompat и материальному дизайну_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>Обзор

Эти инструкции описывают обновить существующие приложения Xamarin.Forms Android будет использовать библиотеку совместимости приложений и включение материальному дизайну в версии приложения Xamarin.Forms для Android.

### <a name="1-update-xamarinforms"></a>1. Обновить Xamarin.Forms

Убедитесь, что решение использует Xamarin.Forms 2.0 или более поздней версии. Обновите пакет Xamarin.Forms Nuget 2.0, если это необходимо.

### <a name="2-check-android-version"></a>2. Проверка версии Android

Убедитесь, что проект Android целевая платформа — Android 6.0 (Marshmallow). Проверьте **проект Android > Параметры > Создать > Общие** выбранные параметры, чтобы убедиться в corrent framework:

 ![](appcompat-images/target-android-6-sml.png "Конфигурации сборки общие Android")

### <a name="3-add-new-themes-to-support-material-design"></a>3. Добавить новые темы для поддержки материальному дизайну

Создайте следующие три файла в проект Android и вставьте в него содержимое ниже. Компания Google предоставляет [руководства по стилю](http://www.google.com/design/spec/style/color.html#color-color-palette) и [цвет палитры генератор](http://www.materialpalette.com/) поможет вам выбрать схему другой цвет в соответствии с указанной.

**Resources/values/colors.xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Resources/values/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
  </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorPrimaryDark">@color/primaryDark</item>
    <item name="colorAccent">@color/accent</item>
    <item name="android:windowBackground">@color/window_background</item>
    <item name="windowActionModeOverlay">true</item>
  </style>
</resources>
```

Дополнительного стиля, которые должны быть включены в **v21 значения** папки, чтобы применить определенные свойства, при запуске Android Lollipop и более поздних версиях.

**Resources/values-v21/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4. Обновление файла AndroidManifest.xml

Чтобы обеспечить эту новую тему сведения является темой используется, и задайте **AndroidManifest** файл, добавив `android:theme="@style/MyTheme"` (оставьте остальную часть данных XML, так как она была).

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. Укажите макеты панели инструментов и вкладку

Создание **Tabbar.axml** и **Toolbar.axml** файлы в **ресурсы/макета** каталоге и вставьте в их содержимое из ниже:

**Resources/layout/Tabbar.axml**

```xml
<android.support.design.widget.TabLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/sliding_tabs"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:tabIndicatorColor="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

Заданы несколько свойств для тех вкладок, включая тяжести вкладки для `fill` и режим `fixed`.
Если у вас много вкладок можно переключиться в это чтобы прокручиваемые - прочтите Android [TabLayout документации](http://developer.android.com/reference/android/support/design/widget/TabLayout.html) для получения дополнительных сведений.

**Resources/layout/Toolbar.axml**

```xml
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:layout_scrollFlags="scroll|enterAlways" />
```

В этих файлах мы создаем конкретной темы для панели инструментов, которые могут быть различными для вашего приложения.
Ссылаться на [Hello инструментов](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/) записи блога, чтобы узнать больше.


### <a name="6-update-the-mainactivity"></a>6. Обновление `MainActivity`

В существующих приложениях Xamarin.Forms **MainActivity.cs** класс будет наследовать от `FormsApplicationActivity`. Его необходимо заменить `FormsAppCompatActivity` для включения новых функциональных возможностей.

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

Наконец, «подключить» новые макеты на шаге 5 в `OnCreate` метод, как показано ниже:

```csharp
protected override void OnCreate(Bundle bundle)
{
  // set the layout resources first
  FormsAppCompatActivity.ToolbarResource = Resource.Layout.Toolbar;
  FormsAppCompatActivity.TabLayoutResource = Resource.Layout.Tabbar;

  // then call base.OnCreate and the Xamarin.Forms methods
  base.OnCreate(bundle);
  Forms.Init(this, bundle);
  LoadApplication(new App());
}
```
