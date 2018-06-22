---
title: Существенные темы
description: Как темы Xamarin.Android приложения с темой материалы
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: a3b5f908330833a38aad9e329835a4a437fc29f0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771324"
---
# <a name="material-theme"></a>Существенные темы

*Существенные темы* является стиль интерфейса пользователя, который определяет внешний вид представления и действий, начиная с Android 5.0 (без описания операций). Существенные темы встроено в Android 5.0, он используется системой пользовательского интерфейса, а также приложениями. Существенные темы не «тема» в смысле параметр внешний вид всей системы, который пользователь может динамически выбирать из меню параметров. Вместо этого темы материал может рассматриваться как набор связанных встроенных базовых стиля, которые можно использовать для настройки внешнего вида приложения.

Android предоставляет три разновидности материал темы:

-  `Theme.Material` &ndash; Версии темной темы материал; Это конфигурация по умолчанию в Android 5.0.

-  `Theme.Material.Light` &ndash; Облегченная версия материал темы.

-  `Theme.Material.Light.DarkActionBar` &ndash; Облегченная версия материал темы, но действие темной линией.

Примеры этих видов материал темы отображаются здесь:

[![Снимки экрана примера темной темой, Светлая тема и панель действий темной темы](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

Можно наследовать от темы материал для создания собственной темы переопределения некоторых или всех атрибутов цвета. Например, можно создать тему, производный от `Theme.Material.Light`, но переопределяет цвет панель приложения, сопоставляющие цвет фирменного стиля. Можно также стиль отдельных представлений; Например, можно создать стиль для [CardView](~/android/user-interface/controls/card-view.md) , более скругленные углы и использует более темным цветом фона.

Можно использовать один темы для всего приложения или для разных экранов в приложении (действия) можно использовать различные темы. На снимках экрана выше например, одно приложение использует другой темы для каждого действия, для демонстрации встроенные цветовые схемы. Переключатели переключитесь в приложение различные действия и в результате отображения различных тем.

Материал темы поддерживаются только в Android 5.0 и более поздних версий, нельзя использовать его (или пользовательские темы, производным от темы материал) тему приложения для выполнения в более ранних версиях Android. Тем не менее, можно настроить приложение для использования темы материал на устройствах Android 5.0 и правильно переключиться на более ранних темы при запуске в старых версиях Android (см. [совместимости](#compatibility) сведения этой статьи).


## <a name="requirements"></a>Требования

Для использования новых возможностей Android 5.0 материал темы приложения на основе Xamarin требуется следующее.

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 или более поздней версии необходимо установить и настроить с помощью Visual Studio или Visual Studio для Mac. 

-  **Пакет SDK для Android** &ndash; Android 5.0 (API 21) должен быть установлен через диспетчер Android SDK.

-  **Java JDK 1.8** &ndash; JDK 1.7 можно использовать, если вы являетесь специально для различных версий API уровня 23 и более ранних версий. JDK 1.8 доступна из [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Дополнительные сведения о настройке проекта приложения Android 5.0 см. в разделе [параметр вверх Android 5.0 проекта](~/android/platform/lollipop.md).


## <a name="using-the-built-in-themes"></a>С помощью встроенных темы

Для использования темы материал проще всего настроить приложение на использование встроенных темы без настройки. Если не нужно явным образом настраивать темы приложения по умолчанию будет `Theme.Material` (Темная тема). Если ваше приложение имеет только одно действие, можно настроить тему на уровне приложения. Если приложение имеет несколько действий, можно настроить тему на уровне приложения таким образом, он использует ту же тему для всех действий или различные действия можно назначить различные темы. Следующих разделах описывается настройка тем на уровне приложения и на уровне действия.


### <a name="theming-an-application"></a>Темы приложения

Чтобы настроить всего приложения для использования flavor материал темы, задайте `android:theme` атрибут узла приложения в **AndroidManifest.xml** одно из следующих действий:

-  `@android:style/Theme.Material` &ndash; Темная тема.

-  `@android:style/Theme.Material.Light` &ndash; Светлая тема.

-  `@android:style/Theme.Material.Light.DarkActionBar` &ndash; Светлая тема действие темной линией.

В следующем примере настраивается приложения *MyApp* использование светлого оформления:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

Кроме того, можно установить приложение `Theme` атрибута в **AssemblyInfo.cs** (или **Properties.cs**). Пример:

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Если задана тема приложения `@android:style/Theme.Material.Light`, каждое действие в *MyApp* будет отображаться при помощи `Theme.Material.Light`.


### <a name="theming-an-activity"></a>Темы действия

К теме действие добавления `Theme` параметру `[Activity]` атрибута выше объявлении действие и назначение `Theme` для flavor материал темы, который вы хотите использовать. Следующий пример темы действие с `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

Другие действия в этом приложении будет использоваться значение по умолчанию `Theme.Material` Темная цветовая схема (или, если задан параметр темы приложения).

<a name="customtheme" />

## <a name="using-custom-themes"></a>С помощью пользовательские темы

Вы можете улучшить фирменного стиля, создав пользовательскую тему стили приложения с фирменного стиля&rsquo;s цвета. Чтобы создать пользовательские темы, определить новый стиль, который является производным от встроенных flavor материал темы, переопределяя атрибуты цвета, которые вы хотите изменить. Например, можно определить пользовательские темы, который является производным от `Theme.Material.Light.DarkActionBar` и примет бежевый вместо белый цвет фона экрана.

Существенные тема предоставляет следующие атрибуты макета для настройки:

-  `colorPrimary` &ndash; Цвет панели приложения.

-  `colorPrimaryDark` &ndash; Цвет строки состояния и полос контекстно-зависимое приложение; Это обычно темной версии `colorPrimary`.

-  `colorAccent` &ndash; Цвет элементов управления пользовательского интерфейса, таких как флажков, переключателей и текстовых полей редактирования.

-  `windowBackground` &ndash; Цвет фона экрана.

-  `textColorPrimary` &ndash; Цвет текста пользовательского интерфейса на панели приложения.

-  `statusBarColor` &ndash; Цвет строки состояния.

-  `navigationBarColor` &ndash; Цвет на панели навигации.

Эти области экрана имеют метки на следующей схеме:

[![Схема атрибуты и их области связанном экране](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

По умолчанию `statusBarColor` присвоено значение `colorPrimaryDark`. Можно задать `statusBarColor` сплошной цвет, и ему можно присвоить `@android:color/transparent` сделать прозрачным в строке состояния. На панели навигации можно также сделать прозрачным, задав `navigationBarColor` для `@android:color/transparent`.


### <a name="creating-a-custom-app-theme"></a>Создание пользовательского приложения темы

Можно создать тему пользовательского приложения путем создания и изменения файлов в **ресурсов** папку проекта приложения. Для настройки стиля приложений с пользовательские темы, выполните следующие действия:

-   Создание **colors.xml** файла в **ресурсы или значений** &mdash; этот файл используется для определения цвета пользовательские темы. Например, можно вставить следующий код в **colors.xml** помогут вам приступить к работе:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   Измените этот пример файла, чтобы определить имена и цветовые коды для цветовые ресурсы, которые будут использоваться в пользовательской темы.

-   Создание **ресурсы, значения v21** папки. В этой папке создайте **styles.xml** файла:

    [![Расположение styles.xml в папке ресурсов, значения 21.xml](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Обратите внимание, что **ресурсы, значения v21** относится к Android 5.0 &ndash; более старых версиях Android не будет читать файлы в этой папке.

-   Добавить `resources` узел **styles.xml** и определить `style` узел с именем пользовательской темы. Например, вот **styles.xml** файл, который определяет *MyCustomTheme* (производный от встроенной `Theme.Material.Light` стиля темы):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   На этом этапе приложение, которое использует *MyCustomTheme* отобразит акции `Theme.Material.Light` темы без настройки:

    [![Внешний вид пользовательскую тему перед настроек](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   Добавление настроек цвета для **styles.xml** этого цвета атрибутов макета, которые требуется изменить. Например, чтобы изменить цвет панели приложения, чтобы `my_blue` и изменять цвет элементов управления пользовательского интерфейса для `my_purple`, добавить цвет переопределениями **styles.xml** , ссылающиеся на ресурсы цвета, настроенные в **colors.xml**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

Эти изменения на месте приложения, использующий *MyCustomTheme* отображает цвет панели приложения в `my_blue` и элементов управления пользовательского интерфейса в `my_purple`, но использовать `Theme.Material.Light` цветовой схемы во всех остальных:

[![Внешний вид пользовательскую тему после настройки](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

В этом примере *MyCustomTheme* занимает цвета из `Theme.Material.Light` для фона цвет, строка состояния и цвета текста, но изменяет цвет панели приложения для `my_blue` и задает цвет переключатель, чтобы `my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Создание стиля настраиваемого представления

Android 5.0 также позволяет вам для стиля отдельного представления. После создания **colors.xml** и **styles.xml** (как описано в предыдущем разделе), можно добавить представление стиль **styles.xml**.
Для стиля отдельного представления, выполните следующие действия:

-   Изменить **Resources/values-v21/styles.xml** и добавьте `style` узел с именем стиля настраиваемого представления. Задать атрибуты пользовательский цвет для представления в рамках этого `style` узла. Например, чтобы создать настраиваемый [CardView](~/android/user-interface/controls/card-view.md) стиль, который более скругленные углы и использует `my_blue` как цвет фона карточки, добавить `style` узел **styles.xml** (внутри `resources`узел) и настроить фоновый цвет и угла radius:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   В макете, задайте `style` атрибутов для этого представления в соответствии с именем пользовательского стиля, выбранного на предыдущем шаге. Пример:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

Следующий снимок экрана примером является значение по умолчанию `CardView` (показано слева), по сравнению с `CardView` , стилем со специальной `CardView.MyBlue` темы (справа):

[![Примеры CardView по умолчанию и пользовательские CardView](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

В этом примере пользовательский `CardView` отображается цветом фона `my_blue` и 18dp скругленных углов.


## <a name="compatibility"></a>Совместимость

Для настройки стиля приложений, чтобы он использует тему материал на Android 5.0, но автоматически возвращается к вниз совместимый стиля в предыдущих версиях Android, выполните следующие действия:

-   Определите пользовательскую тему в **Resources/values-v21/styles.xml** , производный от материала тематического стиля. Пример:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Определите пользовательскую тему в **Resources/values/styles.xml** , является производным от старых темы, но использует то же имя темы как описано выше. Пример:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   В **AndroidManifest.xml**, настройте приложение с именем пользовательские темы. 
    Пример:

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   Кроме того можно оформить конкретное действие с помощью пользовательской темы:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Если тема использует цветов, определенных в **colors.xml** файла, необходимо поместить этот файл в **ресурсы или значений** (вместо **ресурсы, значения v21**), чтобы обе версии пользовательские темы можно доступ к определениям цвет.

Когда ваше приложение запускается на устройстве с Android 5.0, в нем используется определение темы, указанное в **Resources/values-v21/styles.xml**. Если это приложение выполняется на старые устройства Android, он автоматически вернется для определения темы, указанный в **Resources/values/styles.xml**.

Дополнительные сведения о теме более ранних версий Android см. в разделе [альтернативный ресурсы](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Сводка

В этой статье появился новый тип интерфейса пользователя материал темы, содержащийся в Android 5.0 (без описания операций). Оно описано три встроенных квалификаторов материал темы, которые можно использовать для настройки стиля приложений, описано создайте пользовательскую тему для продвижения приложения и он предоставляется пример тему отдельные представления. Наконец в этой статье описано использовании темы материал в приложении, поддерживая обратную совместимость с более ранними версиями Android.



## <a name="related-links"></a>Связанные ссылки

- [ThemeSwitcher (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [Общие сведения о без описания операций](~/android/platform/lollipop.md)
- [CardView](~/android/user-interface/controls/card-view.md)
- [Альтернативные ресурсы](~/android/app-fundamentals/resources-in-android/alternate-resources.md)
- [Ознакомительная версия для разработчиков Android L](http://developer.android.com/preview/index.html)
- [Существенные разработки](http://developer.android.com/preview/material/index.html)
- [Принципы разработки материала](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
- [Поддержка совместимости](http://developer.android.com/preview/material/compatibility.html)
