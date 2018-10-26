---
title: Обработка поворота
description: В этом разделе описываются способы изменения ориентации устройства в Xamarin.Android. Здесь рассматривается, как для работы с системой Android ресурсов автоматически загрузить ресурсы для с ориентацией для конкретного устройства, как программным образом обрабатывать ориентации изменяется.
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: b2361c04ae627dd68d98f9a6bca1238f1694aaa1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118790"
---
# <a name="handling-rotation"></a>Обработка поворота

_В этом разделе описываются способы изменения ориентации устройства в Xamarin.Android. Здесь рассматривается, как для работы с системой Android ресурсов автоматически загрузить ресурсы для с ориентацией для конкретного устройства, как программным образом обрабатывать ориентации изменяется._


## <a name="overview"></a>Обзор

Так как без труда поворачиваются мобильных устройств, встроенные поворота — это стандартная функция в мобильных операционных систем. Android предоставляет развитую структуру для работы с поворотом в приложениях, независимо от пользовательского интерфейса создается декларативно в формате XML, или программно в коде. При автоматически обработка изменений декларативный макета на устройство поворачивается, приложение может выиграть от тесной интеграции с системой Android ресурсов. Программный макета для изменения должны быть обработаны вручную. Это позволяет более точного управления во время выполнения, но за счет дополнительной работы для разработчиков. Приложения также имеют возможность отказаться от действия перезапуска и вручную контролировать изменение ориентации.

В этом руководстве рассматриваются следующие разделы:

-   **Декларативные поворота макета** &ndash; способы использования в системе Android ресурсов для построения поддержкой ориентации экрана приложений, включая способ загрузки макеты и прорисовываемых ресурсов для определенной ориентации.

-   **Программный поворота макета** &ndash; как программно добавить элементы управления, а также как обрабатывать изменения ориентации вручную.


## <a name="handling-rotation-declaratively-with-layouts"></a>Обработка поворота декларативно с помощью макетов

Путем включения файлов в папках, которые соответствуют соглашениям об именовании, Android автоматически загружает соответствующие файлы, при изменении ориентации.
Сюда входит поддержка:

-   *Ресурсы макета* &ndash; Указание, какие файлы макета используются много для каждой ориентации.

-   *Прорисовываемые ресурсы* &ndash; Указание, какие прорисовываемых ресурсов загружаются для каждого расположения.


### <a name="layout-resources"></a>Ресурсы макета

По умолчанию включены в файлы XML Android (AXML) **ресурсы/макета** папки используются для отрисовки представлений для действия. Эта папка ресурсы, используемые для книжной и альбомной ориентации, если нет дополнительных макета ресурсов специально для альбомной ориентации. Рассмотрим структуру проекта, созданным с помощью шаблона проекта по умолчанию:

[![Структура шаблона проекта по умолчанию](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

Этот проект создает один **Main.axml** файл **ресурсы/макета** папки. Если действия `OnCreate` вызывается метод, он увеличивает представление, определенное в **Main.axml,** который объявляет кнопки, как показано в приведенном ниже коде XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="vertical"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
<Button  
  android:id="@+id/myButton"
  android:layout_width="fill_parent" 
  android:layout_height="wrap_content" 
  android:text="@string/hello"/>
</LinearLayout>
```

При повороте устройства на альбомную ориентацию, действия по `OnCreate` снова вызывается метод и имеет одинаковые **Main.axml** файл увеличивается, как показано на следующем снимке экрана:

[![Так же экране, а в альбомной ориентации](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>Ориентация конкретных макетов

В дополнение к папке макета (который по умолчанию книжной и может также быть явно заданы *макета port* , включая папку с именем `layout-land`), приложение можно определить представления, он должен в альбомной ориентации без кода изменения.

Предположим, что **Main.axml** файл содержал следующий код XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is portrait"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

Если папка с именем макета land, который содержит дополнительный **Main.axml** файл добавляется в проект, подобных макета в альбомной ориентации теперь приведет Android загрузки только что добавленного **Main.axml.** Рассмотрим версию Альбомная **Main.axml** файл, содержащий следующий код (для простоты аналогичен книжной по умолчанию версию кода этот XML-документ, но использует другую строку в `TextView`):

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is landscape"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

Выполнение этого кода и поворот устройства с книжной на альбомную демонстрирует новый загрузка XML, как показано ниже:

[![Снимки экрана альбомной и книжной ориентации, печать книжной ориентации](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>Прорисовываемые ресурсы

Во время поворота Android рассматривает прорисовываемые ресурсы аналогичным образом к ресурсам макета. В этом случае система получает прорисовываемых ресурсов из **ресурсы/drawable** и **ресурсы/drawable-land** папок, соответственно.

Предположим, например, проект включает образ с именем Monkey.png в **ресурсы/drawable** папку, где drawable ссылается `ImageView` в формате XML следующим образом:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Дополнительно Предположим, что другая версия **Monkey.png** включается в **ресурсы/drawable-land**. Подобно тому, как с файлами макета, когда устройство находится повернутый drawable изменения для заданной ориентации, как показано ниже:

[![Другая версия Monkey.png, показанный в режимах альбомной и книжной ориентации](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>Программная обработка поворота

Иногда мы определяем макетов в коде. Это может происходить по разным причинам, включая технические ограничения, предпочтений разработчика и т. д. Когда мы программно добавить элементы управления, приложение вручную необходимо учитывать ориентацией устройства, которое обрабатывается автоматически при использовании XML-ресурсов.


### <a name="adding-controls-in-code"></a>Добавление элементов управления в коде

Добавление элементов управления программными средствами, приложению необходимо выполнить следующие действия:

-  Создание макета.
-  Задайте параметры макета.
-  Создание элементов управления.
-  Задайте параметры макета элемента управления.
-  Добавление элементов управления в макет.
-  Макет в качестве представления содержимого.

Например, рассмотрим пользовательский интерфейс, состоящий из одного `TextView` добавляемого элемента управления `RelativeLayout`, как показано в следующем коде.

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
        
  // create TextView control
  var tv = new TextView (this);

  // set TextView's LayoutParameters
  tv.LayoutParameters = layoutParams;
  tv.Text = "Programmatic layout";

  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Этот код создает экземпляр класса `RelativeLayout` класса и задает его `LayoutParameters` свойство. `LayoutParams` Класс является Android способ инкапсуляции, как элементы управления расположены в виде многократного использования. После создания экземпляра макета, элементов управления можно создать и добавить к ним. Элементы управления также имеют `LayoutParameters`, такие как `TextView` в этом примере. После `TextView` создается, добавив его `RelativeLayout` и параметр `RelativeLayout` , которые были выведены представление содержимого приложения, отображения `TextView` как показано:

[![Кнопку приращения счетчика, показанную на книжной и альбомной режимы](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>Обнаружение ориентации в коде

Если приложение пытается загрузить другой пользовательский интерфейс для каждой ориентации при `OnCreate` вызывается (это происходит каждый раз, когда устройство поворачивается), он должен определения ориентации, а затем загрузите код нужного пользовательского интерфейса. В Android имеется класс с именем `WindowManager`, который может использоваться для определения текущего поворот устройства через `WindowManager.DefaultDisplay.Rotation` свойства, как показано ниже:

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
                        
  // get the initial orientation
  var surfaceOrientation = WindowManager.DefaultDisplay.Rotation;
  // create layout based upon orientation
  RelativeLayout.LayoutParams tvLayoutParams;
                
  if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
  } else {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    tvLayoutParams.LeftMargin = 100;
    tvLayoutParams.TopMargin = 100;
  }
                        
  // create TextView control
  var tv = new TextView (this);
  tv.LayoutParameters = tvLayoutParams;
  tv.Text = "Programmatic layout";
        
  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Этот код задает `TextView` быть расположено 100 пикселей от верхней левой части экрана, автоматически анимации в новый макет, при повороте на альбомную, как показано ниже:

[![Состояние представления сохраняется между режимами альбомной и книжной ориентации](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>Предотвращение действие перезапуска

В дополнение к обработке все содержимое `OnCreate`, приложение можно также предотвратить действия перезапускается при изменении ориентации, задав `ConfigurationChanges` в `ActivityAttribute` следующим образом:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
Теперь при повороте устройства, действие не перезапускается. Чтобы вручную обрабатывать при изменении ориентации таким образом, можно переопределить действия `OnConfigurationChanged` метод и определяет ориентацию из `Configuration` объект, передаваемый в, как и в новую реализацию ниже действия:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
public class CodeLayoutActivity : Activity
{
  TextView _tv;
  RelativeLayout.LayoutParams _layoutParamsPortrait;
  RelativeLayout.LayoutParams _layoutParamsLandscape;
                
  protected override void OnCreate (Bundle bundle)
  {
    // create a layout
    // set layout parameters
    // get the initial orientation

    // create portrait and landscape layout for the TextView
    _layoutParamsPortrait = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
                
    _layoutParamsLandscape = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    _layoutParamsLandscape.LeftMargin = 100;
    _layoutParamsLandscape.TopMargin = 100;
                        
    _tv = new TextView (this);
                        
    if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
      _tv.LayoutParameters = _layoutParamsPortrait;
    } else {
      _tv.LayoutParameters = _layoutParamsLandscape;
    }
                        
    _tv.Text = "Programmatic layout";
    rl.AddView (_tv);
    SetContentView (rl);
  }
                
  public override void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
  {
    base.OnConfigurationChanged (newConfig);
                        
    if (newConfig.Orientation == Android.Content.Res.Orientation.Portrait) {
      _tv.LayoutParameters = _layoutParamsPortrait;
      _tv.Text = "Changed to portrait";
    } else if (newConfig.Orientation == Android.Content.Res.Orientation.Landscape) {
      _tv.LayoutParameters = _layoutParamsLandscape;
      _tv.Text = "Changed to landscape";
    }
  }
}
```

Здесь `TextView's` параметры макета инициализируются для альбомной и книжной ориентации. Переменные класса хранения параметров, вместе с `TextView` самой, так как действия не создаются повторно при изменении ориентации. Код по-прежнему использует `surfaceOrientartion` в `OnCreate` для задания начального макета для `TextView`. После этого `OnConfigurationChanged` обрабатывает все последующие макета изменения.

При запуске приложения, Android загружает изменения пользовательского интерфейса, как происходит поворот экрана и не перезапускает действия.


## <a name="preventing-activity-restart-for-declarative-layouts"></a>Предотвращение действие перезапуска для декларативной макетов

Действие перезагрузки, вызванные поворот экрана можно предотвратить также в том случае, если мы определим макета в XML. Например, мы можно использовать этот подход, если нам нужно предотвратить перезапуску действия (из соображений производительности возможно) и нам не нужно загружать новые ресурсы для разных ориентаций.

Чтобы сделать это, мы выполните ту же процедуру, который мы используем со структурой программный. Просто задайте `ConfigurationChanges` в `ActivityAttribute`, как это было сделано `CodeLayoutActivity` ранее. Любой код, который нужно запустить снова при изменении ориентации можно реализовать в `OnConfigurationChanged` метод.


## <a name="maintaining-state-during-orientation-changes"></a>Поддержание состояния во время изменения ориентации

Ли обработка поворота декларативно или программно, все приложения Android должны применять те же способы управления состоянием при изменении ориентации устройства. Управление состоянием важно, так как после перезагрузки выполняющуюся операцию при повороте устройства Android. Android это делается, чтобы упростить процесс загрузки альтернативные ресурсы, такие как макеты и прорисовываемых ресурсов, которые предназначены специально для определенной ориентации. После его перезапуска, действие теряет все временные состояние, в котором он может сохранен в переменные локального класса. Таким образом Если действие является зависимой состояние, его необходимо сохранить свое состояние на уровне приложения. Приложение должно обрабатывать сохранение и восстановление состояния любого приложения, которой необходимо сохранить в изменение ориентации.

Дополнительные сведения о сохранения состояния в Android, см. [жизненный цикл действия](~/android/app-fundamentals/activity-lifecycle/index.md) руководства.


## <a name="summary"></a>Сводка

Эта статья описывает, как использовать встроенные возможности Android для работы с поворотом. Во-первых описывалось использование в системе Android ресурсов для создания приложений ориентации. Затем оно представлено, как добавить элементы управления в коде, а также как обрабатывать изменения ориентации вручную.



## <a name="related-links"></a>Связанные ссылки

- [Демонстрация поворота (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [Жизненный цикл действия](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Обработка изменений среды выполнения](http://developer.android.com/guide/topics/resources/runtime-changes.html)
- [При изменении ориентации экрана быстро](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
