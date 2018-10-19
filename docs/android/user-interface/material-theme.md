---
title: Тема материала
description: Как тему приложения Xamarin.Android с темой материала
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 4b9c39a0ced9a264f501d78142c3bdfd556593ed
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "30771324"
---
# <a name="material-theme"></a>Тема материала

*Тема материала* является стиль интерфейса пользователя, который определяет внешний вид представления и действий, начиная с Android 5.0 (Lollipop). Тема материала встроен в Android 5.0, поэтому он используется системой пользовательского интерфейса, а также приложениями. Тема материала не «темы» в смысле это вариант внешнего вида всей системы, который пользователь может динамически выбирать из меню "Параметры". Вместо этого материала темы может рассматриваться как набор связанных встроенных базовых стилей, которые можно использовать для настройки внешнего вида приложения.

Android предоставляет три вкуса материал темы:

-  `Theme.Material` &ndash; Темно-версию темы материала; Это конфигурация по умолчанию в Android 5.0.

-  `Theme.Material.Light` &ndash; Облегченная версия тема материала.

-  `Theme.Material.Light.DarkActionBar` &ndash; Облегченную версию темы материал, но со строкой темно-действия.

Здесь отображаются примеры этих видов материал темы:

[![Снимки экрана с "темной" теме, "светлой" темой и панели действий Темная тема](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

Можно наследовать от темы материал для создания собственной темы переопределения некоторых или всех атрибутов цвета. Например, можно создать тему, производный от `Theme.Material.Light`, но переопределяет цвет панели приложения в соответствии с цветом вашего бренда. Можно также оформить отдельных представлений; Например, можно создать стиль для [CardView](~/android/user-interface/controls/card-view.md) , более скругленные углы и использует более темным цветом фона.

Можно использовать один темы для всего приложения, или можно использовать различные темы для разных экранов (действия) в приложении. На снимках экрана выше например, одно приложение использует другую тему для каждого действия для демонстрации встроенной цветовой схемы. Переключатели переключитесь в приложение различные действия и, таким образом, отображения различных тем.

Так как тема материала поддерживается только на Android 5.0 и более поздних версий, не позволяет его (или пользовательские темы, производным от темы материал) темы приложения для запуска в более ранних версиях Android. Тем не менее, можно настроить приложение для использования темы материал на устройствах Android 5.0 и корректно переключиться на более ранних темы при запуске в старых версиях Android (см. в разделе [совместимости](#compatibility) этой статьи, Дополнительные сведения).


## <a name="requirements"></a>Требования

Для использования новых функций Android 5.0 материал темы в приложениях на базе Xamarin требуется следующее:

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 или более поздней версии необходимо установить и настроить Visual Studio или Visual Studio для Mac. 

-  **Пакет SDK для Android** &ndash; Android 5.0 (API 21) или более поздней версии необходимо установить через диспетчер пакетов SDK для Android.

-  **Java JDK 1.8** &ndash; JDK 1.7 может использоваться, если вы являетесь специально уровень API — 23 и более ранних версий. JDK 1.8 доступен из [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Чтобы узнать, как настроить проект приложения Android 5.0, см. в разделе [параметр вверх Android 5.0 проекта](~/android/platform/lollipop.md).


## <a name="using-the-built-in-themes"></a>Использование встроенных тем

Самый простой способ использовать тему материал — Настройка приложения для использования встроенных темы без настройки. Если вы не хотите явно настроить тему, приложение по умолчанию `Theme.Material` (Темная тема). Если приложение имеет только одно действие, можно настроить тему на уровне приложения. Если приложение имеет несколько действий, можно настроить тему на уровне приложения таким образом, он использует ту же тему для всех действий или различных тем можно назначить различные действия. В следующих разделах Настройка тем, на уровне приложения и на уровне действий.


### <a name="theming-an-application"></a>Тема приложения

Чтобы настроить приложение целиком, использовать flavor тема материала, задайте `android:theme` атрибут узла приложения в **AndroidManifest.xml** к одному из следующих:

-  `@android:style/Theme.Material` &ndash; Темная тема.

-  `@android:style/Theme.Material.Light` &ndash; Светлая тема.

-  `@android:style/Theme.Material.Light.DarkActionBar` &ndash; Светлая тема с темно-действия.

В следующем примере настраивается приложения *MyApp* использовать "светлой" теме:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

Кроме того, можно установить приложение `Theme` атрибут в **AssemblyInfo.cs** (или **Properties.cs**). Пример:

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Когда задана тема приложения `@android:style/Theme.Material.Light`, каждое действие в *MyApp* будет отображаться при помощи `Theme.Material.Light`.


### <a name="theming-an-activity"></a>Темы мероприятия

Для темы действие, необходимо добавить `Theme` присвоить `[Activity]` атрибут выше объявлении действие и назначение `Theme` для flavor тема материала, который вы хотите использовать. Следующий пример темы действие с `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

Другие действия в этом приложении будет использоваться значение по умолчанию `Theme.Material` темную цветовую схему (или, если настроен параметр темы приложения).

<a name="customtheme" />

## <a name="using-custom-themes"></a>С помощью пользовательские темы

Вы можете повысить свою торговую марку, создание настраиваемой темы, стили приложения с помощью вашего бренда&rsquo;s цвета. Чтобы создать пользовательскую тему, определить новый стиль, который является производным от встроенных flavor тема материала, переопределение атрибутов цвета, которые вы хотите изменить. Например, можно определить пользовательскую тему, производный от `Theme.Material.Light.DarkActionBar` и примет бежевый вместо белый цвет фона экрана.

Тема материала предоставляет следующие атрибуты для настройки макета:

-  `colorPrimary` &ndash; Цвет панели приложения.

-  `colorPrimaryDark` &ndash; Цвет строки состояния и полосы контекстно-зависимое приложение; Это обычно темно-версия `colorPrimary`.

-  `colorAccent` &ndash; Цвет элементов управления пользовательского интерфейса, например флажки, переключатели и редактирования текстовых полей.

-  `windowBackground` &ndash; Цвет фона экрана.

-  `textColorPrimary` &ndash; Цвет текста пользовательского интерфейса на панели приложения.

-  `statusBarColor` &ndash; Цвет строки состояния.

-  `navigationBarColor` &ndash; Цвет панели навигации.

Эти области экрана обозначены на следующей схеме:

[![Схема атрибуты и их области связанном экране](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

По умолчанию `statusBarColor` присваивается значение `colorPrimaryDark`. Можно задать `statusBarColor` со сплошным цветом, или можно разместить его `@android:color/transparent` к прозрачности в строке состояния. На панели навигации можно также сделать прозрачным, задав `navigationBarColor` для `@android:color/transparent`.


### <a name="creating-a-custom-app-theme"></a>Создание пользовательского приложения темы

Можно создать тему пользовательского приложения путем создания и изменения файлов в **ресурсы** папки проекта приложения. Для настройки стиля приложений с пользовательской темы, следуйте инструкциям ниже:

-   Создание **colors.xml** файл **ресурсы и значения, которые** &mdash; этот файл используется для определения цвета пользовательские темы. Например, можно вставить следующий код в **colors.xml** помогут вам приступить к работе:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   Измените этот пример файла, чтобы определить имена и цветовых кодов для цветовые ресурсы, которые будут использоваться в пользовательской темы.

-   Создание **ресурсы/значения v21** папки. В этой папке создайте **styles.xml** файла:

    [![Расположение styles.xml в папке ресурсов/значения 21.xml](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Обратите внимание, что **ресурсы/значения v21** относится только к Android 5.0 &ndash; более старых версиях Android не будет считывать файлы в этой папке.

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

    [![Внешний вид пользовательской темы до настройки](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   Добавить настройки цвета для **styles.xml** задать требуемые цвета атрибутов макета, которые вы хотите изменить. Например, чтобы изменить цвет панели приложения, чтобы `my_blue` и изменить цвет элементов управления пользовательского интерфейса для `my_purple`, добавить цвет переопределения, чтобы **styles.xml** относится цветовые ресурсы, настроенные в **colors.xml**:

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

С изменениями, приложения, использующий *MyCustomTheme* отобразит цвет панели приложения в `my_blue` и элементов управления пользовательского интерфейса в `my_purple`, но использовать `Theme.Material.Light` цветовой схемы во всех остальных:

[![Внешний вид пользовательские темы, после настройки](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

В этом примере *MyCustomTheme* заимствует цветов из `Theme.Material.Light` для фона цвет, строка состояния и цвета текста, но изменяет цвет панели приложения, чтобы `my_blue` и задает цвет переключатель, чтобы `my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Создание стиля настраиваемого представления

Android 5.0 также позволяет вам для стиля отдельного представления. После создания **colors.xml** и **styles.xml** (как описано в предыдущем разделе), можно добавить представление стиль **styles.xml**.
Для определения стиля отдельного представления, следуйте инструкциям ниже:

-   Изменить **Resources/values-v21/styles.xml** и добавьте `style` узел с именем стиля пользовательского представления. Задать атрибуты пользовательский цвет для представления в рамках этого `style` узла. Например, можно создавать пользовательские [CardView](~/android/user-interface/controls/card-view.md) стиль, который более скругленные углы и использует `my_blue` как цвет фона карты, добавить `style` узел **styles.xml** (внутри `resources`узел) и настройте фоновый цвет и угла radius:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   В файле макета задайте `style` атрибута для этого представления, чтобы оно соответствовало имени пользовательский стиль, выбранный на предыдущем шаге. Пример:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

Снимке экрана ниже приведен пример того, значение по умолчанию `CardView` (показано слева) как по сравнению с `CardView` , стилем на пользовательский `CardView.MyBlue` темы (справа):

[![Примеры по умолчанию CardView и Custom CardView](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

В этом примере пользовательский `CardView` отображается цветом фона `my_blue` и 18dp радиус углов.


## <a name="compatibility"></a>Совместимость

Для настройки стиля приложений таким образом, чтобы он использует тему материал на Android 5.0, но автоматически возвращается к стиля вниз совместимой на старых версиях Android, сделайте следующее:

-   Определите пользовательскую тему в **Resources/values-v21/styles.xml** , производный из стиля темы материал. Пример:

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

-   В **AndroidManifest.xml**, настройте приложение с именем пользовательской темы. 
    Пример:

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   Кроме того можно стилистически какого-либо действия с помощью пользовательской темы:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Если тема использует цвета, определенные в **colors.xml** файл, убедитесь, что вы поместите этот файл в **ресурсы и значения, которые** (вместо **ресурсы/значения v21**), чтобы обе версии пользовательской темы можно получить доступ к определениям цвет.

При выполнении приложения на устройстве Android 5.0, он будет использовать определения темы, указанный в **Resources/values-v21/styles.xml**. При этом приложение работает на старые устройства Android, он автоматически вернется к определению темы, указанный в **Resources/values/styles.xml**.

Дополнительные сведения о совместимости темы с более старыми версиями Android, см. в разделе [альтернативные ресурсы](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Сводка

В этой статье представлен новый стиль интерфейса пользователя тема материала, включенные в Android 5.0 (Lollipop). Оно описано три встроенных стиля темы материал, которые можно использовать для настройки стиля приложений, его было рассмотрено, как создать пользовательскую тему фирменной настройки приложения и его предоставленный пример тему отдельного представления. Наконец в этой статье было рассмотрено, как использовать тему материал в приложении, поддерживая обратную совместимость с более старых версиях Android.



## <a name="related-links"></a>Связанные ссылки

- [ThemeSwitcher (пример)](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [Общие сведения о без описания операций](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [Альтернативные ресурсы](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Android без описания операций](https://developer.android.com/about/versions/lollipop)
- [Android круговой разработчика](https://developer.android.com/about/versions/pie/)
- [Материального исполнения](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [Принципы материального исполнения](https://material.io/design/)
- [Поддержка совместимости](https://developer.android.com/training/backward-compatible-ui/)
