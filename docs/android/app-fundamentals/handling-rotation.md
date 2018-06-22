---
title: Обработка поворота
description: В этом разделе описывается изменение ориентации устройства в Xamarin.Android обработки. В этом примере рассматривается с системой Android ресурсов для автоматической загрузки ресурсы для использования конкретного устройства ориентации, как можно программным образом обрабатывать ориентации изменяется.
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1cdc7928c45b99cdd8c8149b3ae9b06e790deeca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30768900"
---
# <a name="handling-rotation"></a>Обработка поворота

_В этом разделе описывается изменение ориентации устройства в Xamarin.Android обработки. В этом примере рассматривается с системой Android ресурсов для автоматической загрузки ресурсы для использования конкретного устройства ориентации, как можно программным образом обрабатывать ориентации изменяется._


## <a name="overview"></a>Обзор

Так, как легко поворачиваются мобильных устройств, встроенные поворота — это стандартная функция мобильных операционных систем. Android предоставляет сложные платформу для задействования поворота в приложениях, независимо от пользовательского интерфейса создается декларативно в формате XML или программно в коде. Автоматически обработки изменения декларативных макета на устройстве, поворачивать, приложения могут выиграть от тесной интеграции с системой Android ресурсов. Программный макет изменения должны обрабатываться вручную. Это позволяет более точного управления во время выполнения, но за счет дополнительной работы для разработчиков. Приложение также можно отказаться от перезапуска действия и ручные контроля над изменение ориентации.

В этом руководстве рассматриваются следующие разделы:

-   **Декларативные поворота макета** &ndash; использование системы Android ресурсов для построения распознает ориентацию экрана приложений, включая способ загрузки макеты и drawables для конкретного ориентации.

-   **Программный поворота макета** &ndash; как программным образом добавлять элементы управления, а также способ обработки изменение ориентации вручную.


## <a name="handling-rotation-declaratively-with-layouts"></a>Обработка поворота декларативно с макетами

Включая файлы в папках, соответствующие соглашениям об именовании, Android автоматически загружает соответствующие файлы при изменении ориентации.
Это обеспечивает поддержку:

-   *Макет ресурсы* &ndash; Указание, какие файлы макета являются завышенными для каждой ориентации.

-   *Ресурсы drawable* &ndash; указание какие drawables загружаются для каждого расположения.


### <a name="layout-resources"></a>Ресурсы макета

По умолчанию включены в файлы Android XML (AXML) **ресурсы и макет** папки, используемые для визуализации представления для действия. Эта папка ресурсы используются для книжной и альбомной ориентации, если отсутствуют дополнительные макета ресурсы предоставляются специально для альбомная. Рассмотрим структура проекта, созданная с помощью шаблона проекта по умолчанию.

[![Структура шаблона проекта по умолчанию](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

Этот проект создает один **Main.axml** файла в **ресурсы и макет** папки. Если действия `OnCreate` вызывается метод, он увеличивает представление, определенное в **Main.axml,** объявляет кнопки, как показано в коде ниже XML:

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

При повороте устройства в альбомной ориентации, действия `OnCreate` повторно вызывается метод и тот же **Main.axml** файла увеличивается, как показано на снимке экрана ниже:

[![Таким же экране, однако в альбомной ориентации](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>Ориентация конкретных макетов

В дополнение к папке макета (которого по умолчанию книжная и также могут быть явно именованными *макета порт* , включая папку с именем `layout-land`), приложение может определить представления, необходимые в альбомной ориентации без кода изменения.

Предположим, что **Main.axml** файл содержал следующий XML-код:

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

Если папка с именем макета земли, которая содержит дополнительный **Main.axml** файл добавляется в проект дало бы ошибку макета в альбомной ориентации теперь приведет загрузке добавленный Android **Main.axml.** Рассмотрим альбомная версии **Main.axml** файл, содержащий следующий код (для простоты этот XML-документ аналогичен Книжная версия по умолчанию код, но использует другую строку в `TextView`):

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

Выполнение этого кода и поворот устройства с книжной на альбомную показаны новые загрузки XML, как показано ниже:

[![Снимки экрана книжной и альбомной печати книжной ориентации](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>Drawable ресурсы

Во время смены Android рассматривает drawable ресурсы аналогично ресурсы макета. В этом случае система получает drawables из **ресурсы или drawable** и **ресурсы или drawable контактном** папки, соответственно.

Например, проект содержит изображение с именем Monkey.png в **ресурсы или drawable** drawable ссылок из папки `ImageView` в формате XML следующим образом:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Дополнительно Предположим, что другая версия **Monkey.png** включается в **ресурсы или drawable контактном**. Точно так же, как и с файлами макета, когда устройство будет поворачивать, drawable изменения для данного ориентацию, как показано ниже:

[![Другая версия Monkey.png, показанный в режиме книжной и альбомной ориентации](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>Обработка поворота программным способом

Иногда мы определяем макеты в коде. Это может произойти по ряду причин, включая технические ограничения, разработчик предпочтения, и т. д. Если программно добавить элементы управления, приложения должны учитывать вручную ориентацией устройства, которое обрабатывается автоматически при использовании XML-ресурсов.


### <a name="adding-controls-in-code"></a>Добавление элементов управления в коде

Добавление элементов управления программными средствами, приложению необходимо выполнить следующие действия:

-  Создание макета.
-  Задайте параметры макета.
-  Создайте элементы управления.
-  Задайте параметры макета элемента управления.
-  Добавьте элементы управления макета.
-  Задайте макет в представлении содержимого.

Например, рассмотрим пользовательский интерфейс, состоящий из одного `TextView` элемент управления, добавленный к `RelativeLayout`, как показано в следующем коде.

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

Этот код создает экземпляр `RelativeLayout` классу и устанавливается его `LayoutParameters` свойство. `LayoutParams` Класса является способом Android инкапсуляции расположение элементов управления в виде для повторного использования. После создания экземпляра макета элементов управления можно создать и добавить к нему. Элементы управления обладают также `LayoutParameters`, такие как `TextView` в этом примере. После `TextView` создается, добавляя его в `RelativeLayout` и параметр `RelativeLayout` отображения приложения в результате представление содержимого `TextView` следующим:

[![Кнопки увеличить счетчик показано в режиме книжной и альбомной ориентации](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>Обнаружение ориентацию в коде

Если приложение пытается загрузить другой интерфейс пользователя для каждой ориентации при `OnCreate` вызывается (это происходит каждый раз при повороте устройства), оно должно определения ориентации и затем загружать код нужного пользовательского интерфейса. Android имеет класс с именем `WindowManager`, который может использоваться для определения текущего поворот устройства через `WindowManager.DefaultDisplay.Rotation` свойства, как показано ниже:

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

Этот код задает `TextView` быть позиционированные 100 пикселей ниже верхней левой части экрана, автоматически анимации в новый макет при повороте Альбомная, как показано ниже:

[![Состояние просмотра будет сохранено в рамках книжной и альбомной режимы](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>Предотвращение действие перезапуска

В дополнение к обработке все содержимое `OnCreate`, приложение можно также предотвратить действия при изменении ориентации, задав в процессе перезагрузки `ConfigurationChanges` в `ActivityAttribute` следующим образом:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
Теперь при повороте устройства действия не запускается. Чтобы вручную обработать изменение ориентации таким образом, можно переопределить действия `OnConfigurationChanged` метод и определяет ориентацию из `Configuration` объект, передаваемый в, как и новая реализация ниже действия:

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

Здесь `TextView's` для альбомной и портретной инициализируются параметры макета. Переменные класса хранения параметров, вместе с `TextView` себя, так как действие не создаются повторно при изменении ориентации. Код по-прежнему использует `surfaceOrientartion` в `OnCreate` для задания начального макета для `TextView`. После этого `OnConfigurationChanged` обрабатывает все последующие макет изменения.

При запуске приложения, Android загружает изменения интерфейса пользователя, как происходит смены устройств и не перезапускает действия.


## <a name="preventing-activity-restart-for-declarative-layouts"></a>Предотвращает перезагрузку действия для декларативного макетов

Перезагрузки действия, вызванные поворот устройства можно избежать, если макет определяется в XML. Например, можно использовать этот подход, чтобы предотвратить перезапуск действия (из соображений производительности возможно) и не требуется загрузить новые ресурсы для различные ориентации.

Чтобы сделать это, мы выполните процедуру, которая используется со структурой программный. Просто установите `ConfigurationChanges` в `ActivityAttribute`, как это делалось `CodeLayoutActivity` ранее. Любой код, который нужно запустить для изменение ориентации снова могут быть реализованы в `OnConfigurationChanged` метод.


## <a name="maintaining-state-during-orientation-changes"></a>Поддержание состояния при изменении ориентации

Ли обработка поворота декларативно или программно, все приложения Android должны применять те же способы управления состоянием при изменении ориентации устройства. Управление состоянием важен, так как после перезагрузки выполняющуюся операцию при повороте устройства Android. Android это делается, чтобы упростить процесс загрузки альтернативный ресурсы, такие как макеты и drawables, предназначенные специально для конкретного ориентации. При перезапуске, действие теряет все переходное состояние, которое он может сохранен в переменные локального класса. Таким образом Если действие требует состояния, должен сохранить свое состояние на уровне приложения. Приложение должно обрабатывать сохранение и восстановление любое состояние приложения, он хочет сохранить через изменения ориентации.

Дополнительные сведения о сохранения состояния в Android посвящены [жизненный цикл действия](~/android/app-fundamentals/activity-lifecycle/index.md) руководства.


## <a name="summary"></a>Сводка

В этой статье рассматривается, как использовать встроенные возможности Android для работы с поворота. Во-первых он описано использование системы Android ресурсов для создания приложений, использующих API ориентации. Затем он представлен способы добавления элементов управления в коде, а также способ обработки изменение ориентации вручную.



## <a name="related-links"></a>Связанные ссылки

- [Демонстрация поворота (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [Жизненный цикл действия](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Обработка изменений среды выполнения](http://developer.android.com/guide/topics/resources/runtime-changes.html)
- [Изменение ориентации экрана быстрый](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
