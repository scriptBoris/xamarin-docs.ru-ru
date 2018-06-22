---
title: Совместимость инструментов
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: c3418a007ded30175049e83d515f59d5134deee0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30768705"
---
# <a name="toolbar-compatibility"></a>Совместимость инструментов


## <a name="overview"></a>Обзор

В этом разделе описывается использование `Toolbar` для версий Android, предшествующих Android 5.0 без описания операций. Если приложение не поддерживает версии Android, более ранних, чем Android 5.0, этот раздел можно пропустить. 

Поскольку `Toolbar` входит в состав библиотеки поддержки Android версии 7, он может использоваться на устройствах под управлением Android 2.1 (API уровня 7) и более поздних версий. Тем не менее [библиотеку поддержки Android версии 7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet должен быть установлен и изменить код таким образом, чтобы он использует `Toolbar` реализацию, предоставляемую в этой библиотеке. В этом разделе объясняется, как установить это NuGet и изменение **ToolbarFun** приложение из [добавления второй панели инструментов](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) , чтобы он запускался в версиях Android, более ранних, чем 5.0 без описания операций.

Изменение приложения для использования версии AppCompat панели инструментов: 

1.  Установите минимальное и Android целевой версии для приложения.

2.  Установка пакета NuGet совместимости приложений.

3.  Используйте темы AppCompat вместо встроенных Android темы.

4.  Изменить `MainActivity` , чтобы его подклассов `AppCompatActivity` вместо `Activity`. 

Каждое из этих действий подробно рассматривается в следующих разделах.



## <a name="set-the-minimum-and-target-android-version"></a>Установка минимума и целевая версия Android

Целевой платформы приложения должен быть не ниже 21 уровень API или приложения не будет выполнять развертывание должным образом. При возникновении ошибки, такие как **идентификатор ресурса не найден для атрибута «tileModeX» в пакете «android»** возникает при развертывании приложения, это вызвано требуемой версии .NET Framework не равно **Android 5.0 (API уровня 21 - без описания операций)**  или выше. 

Значение целевой версии .NET Framework, уровень API 21 уровне или выше, а значение параметров Android API уровня проекта Минимальная версия Android, приложение для поддержки. Дополнительные сведения об установке Android API уровней см. в разделе [основные сведения об уровнях Android API](~/android/app-fundamentals/android-api-levels.md). В `ToolbarFun` примере Минимальная версия Android задано значение KitKat (4.4 уровень API). 


## <a name="install-the-appcompat-nuget-package"></a>Установка пакета NuGet совместимости приложений

Добавьте [библиотеку поддержки Android версии 7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) пакета в проект. В Visual Studio щелкните правой кнопкой мыши **ссылки** и выберите **управление пакетами NuGet...** . Нажмите кнопку **Обзор** и выполните поиск **библиотеку поддержки Android версии 7 AppCompat**. Выберите **Xamarin.Android.Support.v7.AppCompat** и нажмите кнопку **установить**: 

[![Снимок экрана V7 Appcompat пакета, выбранного в управление пакетами NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

При установке этого NuGet нескольких других пакетах NuGet также устанавливаются в случае отсутствия (такие как **Xamarin.Android.Support.Animated.Vector.Drawable**, **Xamarin.Android.Support.v4**, и **Xamarin.Android.Support.Vector.Drawable**). Дополнительные сведения об установке пакетов NuGet см. в разделе [Пошаговое руководство: включая NuGet в проекте](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 


## <a name="use-an-appcompat-theme-and-toolbar"></a>Используйте панель инструментов и AppCompat темы

Библиотека AppCompat поставляется с несколькими `Theme.AppCompat` темы, которые могут использоваться в любой версии Android, поддерживаемые библиотекой совместимости приложений. `ToolbarFun` Тему приложения примере является производным от `Theme.Material.Light.DarkActionBar`, который не является доступной для более ранних версий Android без описания операций. Таким образом `ToolbarFun` должны быть адаптированы для использования аналог AppCompat для этой темы `Theme.AppCompat.Light.DarkActionBar`. Кроме того поскольку `Toolbar` будет недоступно в версиях Android, более ранних, чем без описания операций, необходимо использовать версию AppCompat `Toolbar`. Таким образом, необходимо использовать макеты `android.support.v7.widget.Toolbar` вместо `Toolbar`. 


### <a name="update-layouts"></a>Макеты обновления

Изменить **Resources/layout/Main.axml** и замените `Toolbar` элемент следующий XML-код: 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Изменить **Resources/layout/toolbar.xml** и замените его содержимое следующим кодом XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"/>
```

Обратите внимание, что `?attr` значения больше не начинаются с префикса `android:` (Помните, что `?` нотации ссылается на ресурс в текущей темы). Если `?android:attr` по-прежнему были использованы здесь Android ссылка значение атрибута из текущих платформы, а не из библиотеки совместимости приложений. Так как в этом примере используется `actionBarSize` определяется в библиотеке AppCompat `android:` префикс удаляется. Аналогичным образом `@android:style` изменяется на `@style` , чтобы `android:theme` атрибута задано значение темы в библиотеке AppCompat &ndash; `ThemeOverlay.AppCompat.Dark.ActionBar` темы здесь используется вместо `ThemeOverlay.Material.Dark.ActionBar`. 


### <a name="update-the-style"></a>Измените стиль

Изменить **Resources/values/styles.xml** и замените его содержимое следующим кодом XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="MyTheme.Base"> </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="windowNoTitle">true</item>
    <item name="windowActionBar">false</item>
    <item name="colorPrimary">#5A8622</item>
    <item name="colorAccent">#A88F2D</item>
  </style>
</resources>
```

Имена элементов и родительской темы в этом примере больше не начинаются с префикса `android:` так как мы используем AppCompat библиотеки. Кроме того, изменения темы родительского версии AppCompat `Light.DarkActionBar`. 



### <a name="update-menus"></a>Обновление меню

Для поддержки более ранних версиях Android, библиотека AppCompat использует настраиваемые атрибуты, которые отражают атрибуты `android:` пространства имен. Тем не менее, некоторые атрибуты (такие как `showAsAction` атрибута, используемого в `<menu>` тег) не существуют в платформы Android на старые устройства &ndash; `showAsAction` впервые появился в Android API 11, которая недоступна в Android API 7. По этой причине пользовательского пространства имен необходимо использовать в качестве префикса всех атрибутов, определенных в библиотеке технической поддержки. В меню файлы ресурсов, с именем пространства имен `local` определен в качестве префикса `showAsAction` атрибута. 

Изменить **Resources/menu/top_menus.xml** и замените его содержимое следующим кодом XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       local:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       local:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       local:showAsAction="never"
       android:title="Preferences" />
</menu>
```

`local` Добавляется пространство имен со следующей строки:

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

`showAsAction` Атрибут предшествовало это `local:` имен вместо `android:` 

```csharp
local:showAsAction="ifRoom"
```

Аналогично, изменение **Resources/menu/edit_menus.xml** и замените его содержимое следующим кодом XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

Как предоставляет поддержку для этого параметра пространство имен `showAsAction` атрибут версии Android до 11 уровень API? Настраиваемый атрибут `showAsAction` и все его возможные значения включаются в приложении, при установке AppCompat NuGet. 


## <a name="subclass-appcompatactivity"></a>Подкласс AppCompatActivity

На заключительном этапе преобразования является изменение `MainActivity` , чтобы оно было подкласс `AppCompactActivity`. Изменить **MainActivity.cs** и добавьте следующее `using` инструкции: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Этот код объявляет `Toolbar` быть версии AppCompat `Toolbar`. Затем измените определение класса для `MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

Панели действий присваивается версия AppCompat `Toolbar`, замените вызов `SetActionBar` с `SetSupportActionBar`. В этом примере заголовок также изменяется для указания того, что версия AppCompat `Toolbar` используется:

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

И, наконец измените уровень Android минимальным значением предварительной без описания операций, которое поддерживается (например, API 19). 

Выполните сборку приложения и запустите его на устройстве перед без описания операций или эмулятор Android. На следующем рисунке показан версии AppCompat **ToolbarFun** на 4 хранилища, выполняемое KitKat (API 19): 

[![Сделать снимок полного экрана приложения, запущенного на устройстве KitKat, отображаются обе панели инструментов](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

При использовании библиотеки AppCompat темы не нужно переключаться на основе версии Android &ndash; библиотеки AppCompat дает возможность предоставить целостное взаимодействие с пользователем во всех поддерживаемых версиях Android. 




## <a name="related-links"></a>Связанные ссылки

- [Панель инструментов без описания операций (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Панель инструментов AppCompat (пример)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
