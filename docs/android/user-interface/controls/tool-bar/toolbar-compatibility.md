---
title: Совместимость панели инструментов
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 12c19cf1024b78e8be30b7c9f2652019e9854375
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110333"
---
# <a name="toolbar-compatibility"></a>Совместимость панели инструментов


## <a name="overview"></a>Обзор

В этом разделе описываются способы использования `Toolbar` в версиях Android более ранних, чем Android 5.0 Lollipop. Если приложение не поддерживает версиях Android до Android 5.0, этот раздел можно пропустить. 

Поскольку `Toolbar` является частью библиотеки поддержки Android версии 7, он может использоваться на устройствах под управлением Android 2.1 (API уровня 7) и более поздних версий. Тем не менее [библиотеки поддержки Android версии 7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet должен быть установлен и изменить код таким образом, чтобы он использует `Toolbar` , реализованной в этой библиотеке. В этом разделе объясняется, как установить этот пакет NuGet и изменить **ToolbarFun** приложения из [Добавление второй панели инструментов](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) таким образом, чтобы он выполняется в версиях Android более ранних, чем Lollipop 5.0.

Изменение приложения для использования AppCompat версия панели инструментов: 

1.  Задайте минимальное и целевой объект Android версии для приложения.

2.  Установите пакет NuGet совместимости приложений.

3.  Используйте тему AppCompat вместо встроенных Android темы.

4.  Изменить `MainActivity` таким образом, чтобы его подклассов `AppCompatActivity` вместо `Activity`. 

Каждое из этих действий подробно в следующих разделах.



## <a name="set-the-minimum-and-target-android-version"></a>Установка минимума и целевая версия Android

Целевая платформа приложения должно быть задано, уровень API 21 или больше, или приложение не будет выполнять развертывание должным образом. Если произойдет ошибка, например **идентификатора ресурса не найден для атрибута «tileModeX» в пакете «android»** наблюдается при развертывании приложения, это обусловлено требуемой версии .NET Framework не присвоено значение **Android 5.0 (API уровня 21 - без описания операций)**  или более поздней версии. 

Задайте целевой платформы уровня API уровня 21 или более поздней версии и параметры Android API уровня проекта до минимальной версии Android, которая поддерживает приложения. Дополнительные сведения о настройке уровней Android API см. в разделе [уровнях API Android понимание](~/android/app-fundamentals/android-api-levels.md). В `ToolbarFun` примере минимальной версии Android имеет значение KitKat (4.4 уровень API). 


## <a name="install-the-appcompat-nuget-package"></a>Установите пакет AppCompat NuGet

Затем добавьте [библиотеки поддержки Android версии 7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) пакет в проект. В Visual Studio щелкните правой кнопкой мыши **ссылки** и выберите **управление пакетами NuGet...** . Нажмите кнопку **Обзор** и выполните поиск **библиотеки поддержки Android версии 7 AppCompat**. Выберите **Xamarin.Android.Support.v7.AppCompat** и нажмите кнопку **установить**: 

[![Снимок экрана Appcompat версии 7 пакета, выбранного в управление пакетами NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

Если установлен этот пакет NuGet, несколько других пакетов NuGet также устанавливаются в случае отсутствия (такие как **Xamarin.Android.Support.Animated.Vector.Drawable**, **Xamarin.Android.Support.v4**, и **Xamarin.Android.Support.Vector.Drawable**). Дополнительные сведения об установке пакетов NuGet см. в разделе [Пошаговое руководство: включая NuGet в проекте](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 


## <a name="use-an-appcompat-theme-and-toolbar"></a>Использовать темы AppCompat и панели инструментов

Библиотека AppCompat поставляется с несколькими `Theme.AppCompat` темы, которые могут использоваться в любой версии Android, поддерживаемые библиотекой совместимости приложений. `ToolbarFun` Тему пример приложения является производным от `Theme.Material.Light.DarkActionBar`, он недоступен в версиях, чем без описания операций. Таким образом `ToolbarFun` должны быть адаптированы к использованию какой AppCompat для этой темы `Theme.AppCompat.Light.DarkActionBar`. Кроме того поскольку `Toolbar` — недоступно в версиях Android более ранних, чем без описания операций, необходимо использовать версию AppCompat `Toolbar`. Таким образом, необходимо использовать макеты `android.support.v7.widget.Toolbar` вместо `Toolbar`. 


### <a name="update-layouts"></a>Макеты обновления

Изменить **Resources/layout/Main.axml** и замените `Toolbar` элемент следующим кодом XML: 

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

Обратите внимание, что `?attr` значения больше не начинаются с префикса `android:` (Помните, что `?` нотации ссылается на ресурс в текущей теме). Если `?android:attr` использовались по-прежнему здесь Android будет ссылаться на значение атрибута из текущего выполняемого платформы, а не из библиотеки совместимости приложений. Так как в этом примере используется `actionBarSize` определенные в библиотеке AppCompat `android:` префикс удаляется. Аналогичным образом `@android:style` изменяется на `@style` таким образом, чтобы `android:theme` атрибуту присваивается темы в библиотеке AppCompat &ndash; `ThemeOverlay.AppCompat.Dark.ActionBar` тема здесь используется вместо `ThemeOverlay.Material.Dark.ActionBar`. 


### <a name="update-the-style"></a>Обновление стилей

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

Имена элементов и родительского темы в этом примере больше не начинаются с префикса `android:` так, как мы используем библиотеку совместимости приложений. Кроме того, тема родительского изменяется до версии AppCompat `Light.DarkActionBar`. 



### <a name="update-menus"></a>Обновление меню

Для поддержки более ранних версиях Android, библиотека AppCompat использует настраиваемые атрибуты, которые отражают атрибуты `android:` пространства имен. Тем не менее, некоторые атрибуты (такие как `showAsAction` атрибута, используемого в `<menu>` тег) не существуют на платформе Android, в более старых устройств &ndash; `showAsAction` была введена в Android API 11, но не поддерживается в Android API 7. По этой причине пользовательского пространства имен должны использоваться в качестве префикса все атрибуты, определенные в библиотеке поддержки. В файлах ресурсов меню, именем пространства имен `local` определен в качестве префикса `showAsAction` атрибута. 

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

`local` Пространство имен добавляется со следующей строки:

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

`showAsAction` Атрибут предшествовало это `local:` пространства имен вместо `android:` 

```csharp
local:showAsAction="ifRoom"
```

Аналогичным образом изменить **Resources/menu/edit_menus.xml** и замените его содержимое следующим кодом XML:

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

Каким образом этого параметра пространство имен обеспечивает поддержку `showAsAction` атрибут в версиях Android ниже 11 уровень API? Настраиваемый атрибут `showAsAction` и все его возможные значения включаются в приложении, при установке NuGet совместимости приложений. 


## <a name="subclass-appcompatactivity"></a>Подкласс AppCompatActivity

На заключительном этапе преобразования является изменение `MainActivity` таким образом, чтобы она является подклассом `AppCompactActivity`. Изменить **MainActivity.cs** и добавьте следующие `using` инструкции: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Этот код объявляет `Toolbar` быть AppCompat версию `Toolbar`. Затем измените определение класса для `MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

Для задания на панели действий до версии AppCompat `Toolbar`, замените вызов `SetActionBar` с `SetSupportActionBar`. В этом примере заголовок изменяется также указывает, что версии AppCompat `Toolbar` используется:

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Наконец установите уровень Android минимальное значение предварительного без описания операций, которые должны поддерживаться (например, API 19). 

Выполните сборку приложения и запустите его на устройстве перед Lollipop или эмуляторе Android. На следующем рисунке показаны версии AppCompat **ToolbarFun** на 4 хранилища под управлением KitKat (API 19): 

[![Сделать снимок полного экрана приложения, запущенного на устройстве KitKat, отображаются обе панели инструментов](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

Если библиотека AppCompat, темы не нужно переключаться на основе Android версии &ndash; библиотека AppCompat позволяет предоставить согласованный пользовательский интерфейс во всех поддерживаемых версиях Android. 




## <a name="related-links"></a>Связанные ссылки

- [Панель инструментов без описания операций (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Панель инструментов совместимости приложений (пример)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
