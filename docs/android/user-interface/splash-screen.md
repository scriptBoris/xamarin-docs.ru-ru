---
title: Заставка
description: Приложение Android потребуется некоторое время для запуска, особенно в том случае, если первым запуском приложения на устройстве. Экрана-заставки может отображать start up хода выполнения, пользователя или для обозначения фирменной символики.
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/06/2018
ms.openlocfilehash: f21eca052ef13fd0e3d6efa261e1ff70b3b14372
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120714"
---
# <a name="splash-screen"></a>Заставка

_Приложение Android потребуется некоторое время для запуска, особенно в том случае, если первым запуском приложения на устройстве. Экрана-заставки может отображать start up хода выполнения, пользователя или для обозначения фирменной символики._


## <a name="overview"></a>Обзор

Приложение Android потребуется некоторое время для запуска, особенно при первом запуске приложения на устройстве (иногда это называется _холодный запуск_). Могут отображаться на экране-заставке начать работу хода выполнения для пользователя, или он может отображать сведения о фирме для выявления и рекламы приложения.

В этом руководстве рассматривается один из способов реализации экрана-заставки в приложении Android. Он охватывает следующие действия.

1.  Создание ресурса drawable для экрана-заставки.

2.  Определение новой темы, который будет отображать drawable ресурсов.

3.  Добавление нового действия к приложению, которое будет использоваться в качестве экрана-заставки, определенный в теме, созданные на предыдущем шаге.

[![Пример Xamarin заставку следуют экрана приложения](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)


## <a name="requirements"></a>Требования

В этом руководстве предполагается, что приложение обращается к Android API уровня 15 (Android 4.0.3) или более поздней версии. Приложение должно также иметь **Xamarin.Android.Support.v4** и **Xamarin.Android.Support.v7.AppCompat** добавлен в проект пакеты NuGet.

Весь код и XML в этом руководстве можно найти в [экран-заставка](https://developer.xamarin.com/samples/monodroid/SplashScreen) пример проекта в этом руководстве.


## <a name="implementing-a-splash-screen"></a>Реализация экрана-заставки

— Это самый быстрый способ визуализации и отображения на экране-заставке создайте пользовательскую тему и применить его в действие, в котором видно на экране-заставке. Когда действие отображается, он загружает тему и применяется к фону действия drawable ресурс (ссылается темы). Такой подход позволяет избежать потребность в создании файла разметки.

На экране-заставке реализуется как действие, которое отображает фирменной символикой drawable, выполняет любой инициализации и запуска всех задач. После начальную загрузку приложения, на экране-заставке действие запускает основное действие и удалит себя из приложения стек переходов назад.


### <a name="creating-a-drawable-for-the-splash-screen"></a>Создание Drawable для экрана-заставки

Экран-заставка будет отображаться drawable в качестве фона экрана-заставки действия XML. Бывает необходимо использовать растровый рисунок (например, PNG или JPG) для изображения для отображения.

В этом руководстве мы используем [списка слоев](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) Чтобы центрировать изображение экрана-заставки приложения. Ниже приведен пример `drawable` ресурсов с помощью `layer-list`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

Это `layer-list` будет выровнять изображение заставки по центру **splash.png** на фоне, указанном `@color/splash_background` ресурсов. Поместите этот файл XML в **ресурсы/drawable** папку (например, **Resources/drawable/splash_screen.xml**).

На экране-заставке drawable будет создана, следующим шагом после создания темы для экрана-заставки.


### <a name="implementing-a-theme"></a>Реализация темы

Чтобы создать пользовательскую тему для экрана-заставки действия, изменить (или добавить) файл **values/styles.xml** и создайте новый `style` элемент для экрана-заставки. Пример **values/style.xml** ниже приведен файл с `style` с именем **MyTheme.Splash**:

```xml
<resources>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
  </style>

  <style name="MyTheme" parent="MyTheme.Base">
  </style>

  <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowBackground">@drawable/splash_screen</item>
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowFullscreen">true</item>
  </style>
</resources>
```

**MyTheme.Splash** является очень скудной &ndash; он объявляет фона окна, явно удаляет заголовок из окна и объявляет о весь экран. Если вы хотите создать экран-заставка, который эмулирует пользовательского интерфейса приложения, прежде чем действие увеличивает разметке, можно использовать `windowContentOverlay` вместо `windowBackground` в определении стиля. В этом случае необходимо также изменить **splash_screen.xml** drawable, чтобы он отображал эмуляцию пользовательского интерфейса.


### <a name="create-a-splash-activity"></a>Создание действия заставки

Теперь нам требуется новое действие для Android для запуска, наши изображение заставки и выполняет все задачи запуска. Ниже приведен пример реализации полный заставка экрана.

```csharp
[Activity(Theme = "@style/MyTheme.Splash", MainLauncher = true, NoHistory = true)]
public class SplashActivity : AppCompatActivity
{
    static readonly string TAG = "X:" + typeof(SplashActivity).Name;

    public override void OnCreate(Bundle savedInstanceState, PersistableBundle persistentState)
    {
        base.OnCreate(savedInstanceState, persistentState);
        Log.Debug(TAG, "SplashActivity.OnCreate");
    }

    // Launches the startup task
    protected override void OnResume()
    {
        base.OnResume();
        Task startupWork = new Task(() => { SimulateStartup(); });
        startupWork.Start();
    }

    // Simulates background work that happens behind the splash screen
    async void SimulateStartup ()
    {
        Log.Debug(TAG, "Performing some startup work that takes a bit of time.");
        await Task.Delay (8000); // Simulate a bit of startup work.
        Log.Debug(TAG, "Startup work is finished - starting MainActivity.");
        StartActivity(new Intent(Application.Context, typeof (MainActivity)));
    }
}
```

`SplashActivity` явным образом использует темы, который был создан в предыдущем разделе, переопределение тему по умолчанию приложения.
Нет необходимости загрузить макет в `OnCreate` как темы объявляет drawable, что и фон.

Очень важно задать `NoHistory=true` таким образом, чтобы действия удаляется из стек переходов назад. Чтобы предотвратить Отмена процесса запуска "Назад", можно также переопределить `OnBackPressed` , который будет ничего не делать:

```csharp
public override void OnBackPressed() { }
```

Работа запуска выполняется асинхронно в `OnResume`. Это необходимо, чтобы работы запуска замедлить или не откладывать внешний вид на экране запуска. После завершения работы `SplashActivity` приведет к запуску `MainActivity` и пользователь может начать взаимодействие с приложением.

Этот новый `SplashActivity` устанавливается как действие средства запуска для приложения, задав `MainLauncher` атрибут `true`. Так как `SplashActivity` теперь операции запуска, необходимо изменить `MainActivity.cs`и удалите `MainLauncher` из атрибута `MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>Альбомный режим

В режиме книжной и альбомной ориентации экрана-заставки, реализованы в предыдущих шагах будут отображаться правильно. Однако в некоторых случаях бывает необходимо иметь отдельный заставок для режимов альбомной и книжной ориентации (например, если изображение заставки весь экран).

Чтобы добавить экран-заставка для альбомной ориентации, следуйте инструкциям ниже:

1. В **ресурсы/drawable** папки, добавьте альбомная версию изображение экрана-заставки, который вы хотите использовать. В этом примере **splash_logo_land.png** — это версия альбомная логотип, который использовался в приведенных выше примерах (он использует белым текстом а не синий цвет).

2. В **ресурсы/drawable** папки, создать версию Альбомная `layer-list` drawable, заданное ранее (например, **splash_screen_land.xml**). В этом файле задайте путь растрового изображения в альбомной ориентации версию изображение экрана-заставки. В следующем примере **splash_screen_land.xml** использует **splash_logo_land.png**:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
      <item>
        <color android:color="@color/splash_background"/>
      </item>
      <item>
        <bitmap
            android:src="@drawable/splash_logo_land"
            android:tileMode="disabled"
            android:gravity="center"/>
      </item>
    </layer-list>
    ```

3.  Создание **ресурсы/значения land** папку, если он еще не существует.

4.  Добавьте файлы **colors.xml** и **style.xml** для **значения land** (их можно скопировать и изменить существующий **values/colors.xml**и **values/style.xml** файлов).

5.  Изменить **значения land/style.xml** , заняв альбомная версию drawable для `windowBackground`. В этом примере **splash_screen_land.xml** используется:

    ```xml
    <resources>
      <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
      </style>
        <style name="MyTheme" parent="MyTheme.Base">
      </style>
      <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@drawable/splash_screen_land</item>
        <item name="android:windowNoTitle">true</item>  
        <item name="android:windowFullscreen">true</item>  
        <item name="android:windowContentOverlay">@null</item>  
        <item name="android:windowActionBar">true</item>  
      </style>
    </resources>
    ```

6.  Изменить **значения land/colors.xml** для настройки цветов, которые вы хотите использовать версию альбомной ориентации экрана-заставки. В этом примере на синий для альбомной ориентации изменяется цвет фона для заставки:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
      <color name="primary">#2196F3</color>
      <color name="primaryDark">#1976D2</color>
      <color name="accent">#FFC107</color>
      <color name="window_background">#F5F5F5</color>
      <color name="splash_background">#3498DB</color>
    </resources>
    ```

7.  Постройте и запустите приложение снова. Повороте устройство на альбомную режиме, хотя по-прежнему отображается экран-заставка. На экране-заставке изменения альбомная версии:

    [![Поворот экрана-заставки в режим альбомной ориентации](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)


Обратите внимание на то, что использование альбомной ориентации экрана-заставки не всегда предоставляет удобную работу. По умолчанию Android, приложение запустится в книжной ориентации и переводит его в альбомную режиме, даже если устройство уже находится в альбомной ориентации. Таким образом Если приложение запускается, пока устройство находится в режиме альбомной ориентации, устройство кратко представлены на экране-заставке книжной ориентации, а затем выполняет анимацию вращения от книжной к альбомной ориентации экрана-заставки. К сожалению, этот исходный книжной к альбомной переход происходит, даже если `ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape` указывается в флаги действия заставки. Лучший способ обойти это ограничение является создание одной заставки, выполняющий визуализацию правильно в режиме книжной и альбомной ориентации.


## <a name="summary"></a>Сводка

В этом руководстве рассматривается один из способов реализации экрана-заставки в приложениях Xamarin.Android; а именно применение пользовательской темы действие запуска.


## <a name="related-links"></a>Связанные ссылки

- [Экран-заставка (пример)](https://developer.xamarin.com/samples/monodroid/SplashScreen)
- [Drawable списка слоев](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [ Шаблоны материального исполнения-экранов запуска](https://material.io/design/communication/launch-screen.html#usage)
