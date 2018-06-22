---
title: Создание Циферблат часов
description: В этом руководстве объясняется, как реализовать службу пользовательского наблюдения за лицевой стороны для Android с. Приводятся пошаговые инструкции для создания открытой работу службы начертания цифровой контрольных значений, следуют дополнительный код для создания стиля аналогом Циферблат часов.
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: c02755cc3ff5b46a5a97b6c14185794d8ad538d8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30772943"
---
# <a name="creating-a-watch-face"></a>Создание Циферблат часов

_В этом руководстве объясняется, как реализовать службу пользовательского наблюдения за лицевой стороны для Android с. Приводятся пошаговые инструкции для создания открытой работу службы начертания цифровой контрольных значений, следуют дополнительный код для создания стиля аналогом Циферблат часов._

## <a name="overview"></a>Обзор 

В этом пошаговом руководстве для демонстрации необходимые действия для создания пользовательских Android носят Циферблат часов создания службы начертания основные контрольные значения. Служба начертания начальное Контрольное значение отображает простой цифровой Контрольные значения, отображается текущее время в часах и минутах: 

[![Цифровой циферблате](creating-a-watchface-images/01-initial-face.png "снимок экрана: пример начальной цифровой Циферблат часов")](creating-a-watchface-images/01-initial-face.png#lightbox)

После этого цифровой Циферблат часов созданы и протестированы, дополнительные будет добавлен код, чтобы обновить его до более сложных аналогового Циферблат часов с тремя руки: 

[![Аналоговый Циферблат часов](creating-a-watchface-images/02-example-watchface.png "снимок экрана: пример окончательного аналогового Циферблат часов")](creating-a-watchface-images/02-example-watchface.png#lightbox)

Контрольное значение лицевой стороны службы объединяются в один пакет и устанавливаются как часть приложения одежды. В следующих примерах `MainActivity` содержит не более чем код из шаблона приложения одежды, чтобы службы начертания контрольных значений можно упаковать и развернуть смарт-Контрольное значение как часть приложения. По сути это приложение будет служить исключительно Получение службы начертания Контрольные значения загружаются в износ устройство (или эмулятор), в средство для отладки и тестирования. 

## <a name="requirements"></a>Требования

Для реализации службы начертания контрольных значений, выполнении следующих условий:

-   Android 5.0 (уровень API 21) или более поздней версии на износ устройство или эмулятор.

-   [Библиотеки поддержки Android с Xamarin](https://www.nuget.org/packages/Xamarin.Android.Wear) необходимо добавить в проект Xamarin.Android. 

Несмотря на то, что Android 5.0 минимальное API уровня по внедрению лицевой стороны службы наблюдения за Android 5.1 или более поздней версии рекомендуется использовать. Android носят устройства под управлением Android 5.1 (API 22) или более поздней версии разрешить износ приложения для управления, отображаемых на экране во время устройство находится в маломощных *окружения* режим. При удалении устройства из маломощных *окружения* режиме, он находится в *интерактивный* режим. Дополнительные сведения о режимах см. в разделе [сохранение Your App видимым](https://developer.android.com/training/wearables/apps/always-on.html). 


## <a name="start-an-app-project"></a>Запуск проекта приложения

Создайте новый проект Android с именем **WatchFace** (Дополнительные сведения о создании новых проектов Xamarin.Android см. в разделе [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Диалоговое окно нового проекта](creating-a-watchface-images/03-wear-project-vs-sml.png "выберите носят приложение в диалоговое окно нового проекта")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Диалоговое окно нового проекта](creating-a-watchface-images/03-wear-project-xs-sml.png "выберите носят приложение в диалоговое окно нового проекта")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


Задайте имя пакета `com.xamarin.watchface`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Параметр имени пакета](creating-a-watchface-images/04-package-name-vs.png "присвоено имя пакета com.xamarin.watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Параметр имени пакета](creating-a-watchface-images/04-package-name-xs.png "присвоено имя пакета com.xamarin.watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Кроме того, прокрутите список вниз и включите **INTERNET** и **WAKE_LOCK** разрешения: 

[![Разрешения, необходимые](creating-a-watchface-images/05-required-permissions-vs.png "включить ИНТЕРНЕТА и WAKE_LOCK разрешения")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Задайте минимальный Android версию **Android 5.1 (уровень API 22)**. Кроме того, включите **Internet** и **WakeLock** разрешения:

[![Разрешения, необходимые](creating-a-watchface-images/05-required-permissions-xs.png "включить Интернета и WakeLock разрешения")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

Затем загрузить [preview.png](creating-a-watchface-images/preview.png) &ndash; будет добавляться к **drawables** папку далее в этом пошаговом руководстве.


## <a name="add-the-xamarin-android-wear-package"></a>Добавьте пакет износ Xamarin Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Запустить диспетчер пакетов NuGet (в Visual Studio щелкните правой кнопкой мыши **ссылки** в **обозревателе решений** и выберите **управление пакетами NuGet...** ). Обновление проекта до последней стабильной версии **Xamarin.Android.Wear**: 

[![Добавление диспетчера пакетов NuGet](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "добавить пакет Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

Далее, если **Xamarin.Android.Support.v13** будет установлено, удалите его:

[![Удаление диспетчера пакетов NuGet](creating-a-watchface-images/07-uninstall-v13-sml.png "удаление Xamarin.Support.v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Запустить диспетчер пакетов NuGet (в Visual Studio для Mac, щелкните правой кнопкой мыши **пакетов** в **область решений** и выберите **Добавление пакетов** ). Обновление проекта до последней стабильной версии **Xamarin.Android.Wear**: 

[![Добавление диспетчера пакетов NuGet](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "добавить пакет Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


Построение и запуск приложения на износ устройство или эмулятор (Дополнительные сведения о том, как это сделать см. в разделе [Приступая к работе](~/android/wear/get-started/index.md) руководства). Появится следующий экран приложения на устройстве износ:

[![Снимок экрана приложения](creating-a-watchface-images/08-app-screen.png "экран приложения на устройстве износ")](creating-a-watchface-images/08-app-screen.png#lightbox)

На этом этапе базовое приложение износ имеет функциональных возможностей лицевой стороны Контрольные значения, так как он пока не предоставляет реализации службы начертания контрольных значений. Эта служба будет добавлена далее. 

 
## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android реализует износ смотреть гарнитуры через `CanvasWatchFaceService` класса. `CanvasWatchFaceService` является производным от `WatchFaceService`, который сам является производным от `WallpaperService` как показано на следующей схеме: 

[![Диаграмма наследования](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService диаграмма наследования")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` включает в себя вложенную `CanvasWatchFaceService.Engine`; он создает `CanvasWatchFaceService.Engine` объект, который содержит фактические трудозатраты рисования Циферблат часов. `CanvasWatchFaceService.Engine` является производным от `WallpaperService.Engine` как показано на схеме выше. 

На этой диаграмме не отображаются — `Canvas` , `CanvasWatchFaceService` используется для рисования Циферблат часов &ndash; это `Canvas` передается через `OnDraw` метода, как описано ниже. 

В следующих разделах будут создаваться службы начертания пользовательских контрольных значений, выполните следующие действия: 

1.  Определите класс с именем `MyWatchFaceService` , производный от `CanvasWatchFaceService`. 

2.  В пределах `MyWatchFaceService`, создать вложенный класс с именем `MyWatchFaceEngine` , производный от `CanvasWatchFaceService.Engine`. 

3.  В `MyWatchFaceService`, реализовать `CreateEngine` метод, создающий экземпляр `MyWatchFaceEngine` и возвращает его.

4.  В `MyWatchFaceEngine`, реализовать `OnCreate` метод для создания стиля грани контрольных значений и выполнения других задач инициализации. 

5.  Реализуйте `OnDraw` метод `MyWatchFaceEngine`. Этот метод вызывается при необходимости перерисовки Циферблат часов (т. е. *недействительными*). `OnDraw` Представляет метод, который рисует (и перерисовывает) элементы начертания Контрольные значения, такие как час, минуту и второй руки. 

6.  Реализуйте `OnTimeTick` метод `MyWatchFaceEngine`. 
    `OnTimeTick` вызывается по крайней мере один раз в минуту (в окружающей среды и в интерактивном режиме) или изменении даты и времени. 

Дополнительные сведения о `CanvasWatchFaceService`, в разделе Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) документации по API.
Аналогичным образом [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) объясняет фактическую реализацию Циферблат часов.


### <a name="add-the-canvaswatchfaceservice"></a>Добавить CanvasWatchFaceService

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Добавьте новый файл с именем **MyWatchFaceService.cs** (в Visual Studio щелкните правой кнопкой мыши **WatchFace** в **обозревателе решений**, нажмите кнопку **Добавить > новый элемент...** и выберите **класса**).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Добавьте новый файл с именем **MyWatchFaceService.cs** (в Visual Studio для Mac, щелкните правой кнопкой мыши **WatchFace** проекта, нажмите кнопку **Добавить > новый файл...** и выберите **пустым классом**). 

-----

Замените содержимое этого файла следующим кодом: 

```csharp
using System;
using Android.Views;
using Android.Support.Wearable.Watchface;
using Android.Service.Wallpaper;
using Android.Graphics;

namespace WatchFace
{
    class MyWatchFaceService : CanvasWatchFaceService
    {
        public override WallpaperService.Engine OnCreateEngine()
        {
            return new MyWatchFaceEngine(this);
        }

        public class MyWatchFaceEngine : CanvasWatchFaceService.Engine
        {
            CanvasWatchFaceService owner;
            public MyWatchFaceEngine (CanvasWatchFaceService owner) : base(owner)
            {
                this.owner = owner;
            }
        }
    }
}
```

`MyWatchFaceService` (производный от `CanvasWatchFaceService`) является «основной программы» Циферблат часов. `MyWatchFaceService` реализует только один метод `OnCreateEngine`, который создает и возвращает `MyWatchFaceEngine` объекта (`MyWatchFaceEngine` является производным от `CanvasWatchFaceService.Engine`). Созданный экземпляр `MyWatchFaceEngine` объект должен возвращаться в `WallpaperService.Engine`. Инкапсуляция `MyWatchFaceService` объекта, переданного в конструктор. 

`MyWatchFaceEngine` Представляет реализацию начертания фактическое Контрольные значения &ndash; содержит код, который рисует Циферблат часов. Он также обрабатывает системные события, такие как изменения экрана (внешнюю/интерактивного режима экрана выключение и т. д.). 

 
### <a name="implement-the-engine-oncreate-method"></a>Реализация метода OnCreate ядра

`OnCreate` Метод инициализирует циферблат часов. Добавьте в поле для `MyWatchFaceEngine`: 

```csharp
Paint hoursPaint;
```

Это `Paint` объект будет использоваться для рисования текущее время на Циферблат часов. Добавьте следующий метод `MyWatchFaceEngine`: 

```csharp
public override void OnCreate(ISurfaceHolder holder)
{
    base.OnCreate (holder);

    SetWatchFaceStyle (new WatchFaceStyle.Builder(owner)
        .SetCardPeekMode (WatchFaceStyle.PeekModeShort)
        .SetBackgroundVisibility (WatchFaceStyle.BackgroundVisibilityInterruptive)
        .SetShowSystemUiTime (false)
        .Build ());

    hoursPaint = new Paint();
    hoursPaint.Color = Color.White;
    hoursPaint.TextSize = 48f;
}
```

`OnCreate` вызывается вскоре после `MyWatchFaceEngine` запущена. Он устанавливает `WatchFaceStyle` (контроль взаимодействие с пользователем износ устройства) и создает `Paint` объекта, который будет использоваться для отображения времени. 

Вызов `SetWatchFaceStyle` делает следующее:

1.  Наборы *режим показа* для `PeekModeShort`, вследствие чего уведомления в виде карт небольшой «просмотр» на экране. 

2.  Задает видимость фона `Interruptive`, вследствие чего фон peek карту для отображения кратко только если он представляет прерывающихся уведомления по электронной.

3.  Отключает время пользовательского интерфейса системы по умолчанию, из которых выводится на Циферблат часов, чтобы пользовательские Циферблат часов вместо отображения времени.

Дополнительные сведения об этих и другие параметры стилей начертания Контрольные значения в разделе Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) документации по API.

После `SetWatchFaceStyle` завершения `OnCreate` создает `Paint` объекта (`hoursPaint`) и задает ее цвет белым цветом и его размер шрифта до 48 пикселов ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) должен быть указан в пикселях). 


### <a name="implement-the-engine-ondraw-method"></a>Реализуйте метод OnDraw ядра

`OnDraw` Метода, возможно, является наиболее важным `CanvasWatchFaceService.Engine` метод &ndash; он является методом, фактически Рисование посмотрите начертания элементы, такие как цифр и clock начертания руки. В следующем примере он рисует строку времени на Циферблат часов.
Добавьте следующий метод `MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str, 
        (float)(frame.Left + 70), 
        (float)(frame.Top  + 80), hoursPaint);
}
```

При вызове метода Android `OnDraw`, оно передает `Canvas` экземпляра и границы, в которых можно рисовать начертание шрифта. В приведенном выше примере кода `DateTime` используется для вычисления текущего времени в часах и минутах (в 12-часовом формате). Результирующая строка времени рисуется на холсте с помощью `Canvas.DrawText` метод. Строки будут отображаться 70 пикселей на от левого края и 80 пикселей вниз от верхнего края. 

Дополнительные сведения о `OnDraw` метода, в разделе Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html#onDraw(android.graphics.Canvas, android.graphics.Rect)) документации по API.


### <a name="implement-the-engine-ontimetick-method"></a>Реализуйте метод OnTimeTick ядра

Android периодически вызывает `OnTimeTick` метод обновления времени, отображаемого элементом Циферблат часов. Вызывается в как минимум один раз в минуту (в окружающей среды и интерактивном режимах), или при изменении даты и времени и часового пояса. Добавьте следующий метод `MyWatchFaceEngine`: 

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Эта реализация `OnTimeTick` просто вызывает `Invalidate`. `Invalidate` Расписания метод `OnDraw` перерисовка Циферблат часов. 

Дополнительные сведения о `OnTimeTick` метода, в разделе Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) документации по API.

 
## <a name="register-the-canvaswatchfaceservice"></a>Зарегистрировать CanvasWatchFaceService

`MyWatchFaceService` должен быть зарегистрирован в **AndroidManifest.xml** из связанного приложения одежды. Чтобы сделать это, добавьте следующий XML-код для `<application>` раздела: 

```xml
<service 
    android:name="watchface.MyWatchFaceService" 
    android:label="Xamarin Sample" 
    android:allowEmbedded="true" 
    android:taskAffinity="" 
    android:permission="android.permission.BIND_WALLPAPER">
    <meta-data 
        android:name="android.service.wallpaper" 
        android:resource="@xml/watch_face" />
    <meta-data 
        android:name="com.google.android.wearable.watchface.preview" 
        android:resource="@drawable/preview" />
    <intent-filter>
        <action android:name="android.service.wallpaper.WallpaperService" />
        <category android:name="com.google.android.wearable.watchface.category.WATCH_FACE" />
    </intent-filter>
</service>
```

Этот XML-код выполняет следующие задачи.

1.  Наборы `android.permission.BIND_WALLPAPER` разрешение. Это разрешение дает Контрольное значение лицевой стороны службы разрешение на изменение wallpaper системы на устройстве. Обратите внимание, что это разрешение должно задаваться в `<service>` раздела, а не во внешнем `<application>` раздела. 

2.  Определяет `watch_face` ресурсов. Этот ресурс находится короткое XML-файл, который объявляет `wallpaper` ресурсов (этот файл создается в следующем разделе). 

3.  Объявляет drawable изображение под названием `preview` , отобразится экран выбора выбора контрольных значений. 

4.  Включает в себя `intent-filter` чтобы знать, что Android `MyWatchFaceSevice` будет отображаться Циферблат часов. 

Базовый код, которая завершается `WatchFace` примере. Следующим шагом является добавление необходимых ресурсов.

 
## <a name="add-resource-files"></a>Добавить файлы ресурсов

Перед запуском службы Контрольные значения, необходимо добавить **watch_face** ресурсов и изображений. Во-первых, создайте новый XML-файл в **Resources/xml/watch_face.xml** и замените его содержимое следующим кодом XML: 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Укажите действие при построении этот файл **AndroidResource**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Действие построения](creating-a-watchface-images/10-android-resource-vs.png "набор действие для AndroidResource сборки")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Действие построения](creating-a-watchface-images/10-android-resource-xs.png "набор действие для AndroidResource сборки")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Этот файл ресурсов определяет простой `wallpaper` элемент, который будет использоваться для Циферблат часов. 

Если еще не сделано, загрузите [preview.png](creating-a-watchface-images/preview.png). Установите его на **Resources/drawable/preview.png**. Убедитесь, что этот файл, чтобы добавить `WatchFace` проекта. Это изображение предварительного просмотра отображается для пользователей в средстве выбора начертания Контрольные значения на устройстве одежды. Чтобы создать изображения предварительного просмотра для Циферблат часов, можно снимки экрана Циферблат часов, пока она запущена. (Дополнительные сведения о получении снимки экрана с устройств износ см [Получение снимков экрана](~/android/wear/deploy-test/debug-on-device.md#screenshots)). 


## <a name="try-it"></a>Попробуйте!

Построение и развертывание приложения на устройстве одежды. Вы увидите экране приложения износ отображаются как раньше. Выполните следующую команду, чтобы включить новый Циферблат часов. 

1.  Смахните экран вправо, пока не увидите фона окна контрольных значений. 

2.  Коснитесь и в любом месте хранения на заднем плане экрана для двух секунд.

3.  Проведите слева направо справочников различные фрагменты Контрольное значение. 

4.  Выберите **Xamarin пример** смотреть лицевой стороны (справа): 

    [![Выбор Watchface](creating-a-watchface-images/11-watchface-picker.png "проведите искомый образец Xamarin Циферблат часов")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  Коснитесь **Xamarin пример** смотреть фрагмент, чтобы выбрать его. 

При этом изменяется Циферблат часов износ устройства, чтобы использовать службу начертания пользовательских контрольных значений, реализованные: 

[![Цифровой циферблате](creating-a-watchface-images/12-digital-watchface.png "настраиваемый запуск на устройстве износ цифровой Контрольное значение")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Это довольно грубый Циферблат часов из-за реализации приложения таким образом минимален (например, они не включают фона лицевой стороны контрольных значений и он не вызывает `Paint` методы сглаживания для улучшения внешнего вида). Однако он реализует элементарного функциональные возможности, необходимые для создания пользовательских Циферблат часов. 

В следующем разделе этого Циферблат часов обновляется до более сложные реализации. 

 
## <a name="upgrading-the-watch-face"></a>Обновление Циферблат часов

В оставшейся части этого пошагового руководства `MyWatchFaceService` обновляется для отображения стиле аналогом Циферблат часов и расширяется для поддержки дополнительных функций. Для создания обновленного Циферблат часов будут добавлены следующие возможности: 

1.  Указывает время с аналогового час, минуту и второй руки.

2.  Реагирует на изменения видимости.

3.  Реагирует на изменения между внешней и интерактивный режим. 

4.  Считывает свойства в базовое устройство одежды.

5.  Автоматически обновляет время, когда происходит изменение часового пояса. 

Перед реализацией ниже изменения кода, загрузите [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), распакуйте его и перемещение распакованную PNG-файлы для **ресурсы или drawable** (заменяет предыдущую **preview.png**). Добавить новый PNG-файлы для `WatchFace` проекта.


### <a name="update-engine-features"></a>Обновить функции ядра СУБД

Следующим шагом является обновление **MyWatchFaceService.cs** к реализации, который рисует аналогового Циферблат часов и поддерживает новые функции. Замените содержимое **MyWatchFaceService.cs** с аналогового версии кода начертания Контрольные значения в [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (можно вырезать и вставить этот источник с существующим  **MyWatchFaceService.cs**). 

Эта версия **MyWatchFaceService.cs** добавляет дополнительный код в существующих методов и включает дополнительные переопределенные методы для добавления дополнительных функций. В следующих разделах обзором исходного кода.

#### <a name="oncreate"></a>OnCreate

Обновленный **OnCreate** метод настраивает стиль грани Контрольные значения, как и раньше, но оно включает некоторые дополнительные действия: 

1.  Задает фоновое изображение **xamarin_background** ресурс, который находится в **Resources/drawable-hdpi/xamarin_background.png**. 

2.  Инициализирует `Paint` объектов для рисования часовой стрелки, минутной и секундной стрелки.

3.  Инициализирует `Paint` объект для рисования тактов часов по краям Циферблат часов. 

4.  Создает таймер, который вызывает метод `Invalidate` метод (перерисовку), чтобы вручную секунду перерисовывается каждую секунду. Обратите внимание, что этот таймер необходимо из-за `OnTimeTick` вызовы `Invalidate` только один раз на каждую минуту.

В этом примере включает только один **xamarin_background.png** образ; тем не менее, можно создать различные фоновое изображение для каждого экрана плотности, которое будет поддерживать пользовательский Циферблат часов. 

#### <a name="ondraw"></a>OnDraw

Обновленный **OnDraw** метод формирует стиле аналогом Циферблат часов, выполнив следующие действия: 

1.  Возвращает текущее время, которое теперь хранится в `time` объекта. 

2.  Определяет границы поверхность рисования и ее центра.

3.  Рисует фон, по размеру страницы устройства при рисовании фона.

4.  Рисует двенадцать *тактов* вокруг лицевой стороны часов (соответствующий на Циферблат часов). 

5.  Вычисляет угол, поворот и длина каждой из стрелок контрольных значений.

6.  Рисует каждой стрелки в рабочей области контрольных значений. Обратите внимание, если часы находятся в режиме внешнего секундной стрелки не рисуется. 

 
#### <a name="onpropertieschanged"></a>OnPropertiesChanged

Этот метод вызывается для информирования `MyWatchFaceEngine` о свойствах одежды устройства (например low разрядном режиме окружения и темп работ в защиты). В `MyWatchFaceEngine`, этот метод проверяет только для низкой разрядном режиме окружающей среды (в режиме внешнего младший бит экрана поддерживает меньшее число битов для каждого цвета). 

Дополнительные сведения об этом методе см. в разделе Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) документации по API.


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Этот метод вызывается в том случае, когда устройство износ вводит или выходит из режима окружения. В `MyWatchFaceEngine` реализации Циферблат часов отключает сглаживания, когда он находится в режиме окружения. 

Дополнительные сведения об этом методе см. в разделе Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) документации по API.


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

Этот метод вызывается каждый раз, когда часами становится видимым или скрытым. В `MyWatchFaceEngine`, этот метод регистрирует или отменяет регистрацию получатель часовой пояс (как описано ниже) в соответствии с состояние видимости. 

Дополнительные сведения об этом методе см. в разделе Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) документации по API.

 
### <a name="time-zone-feature"></a>Компонент часового пояса

Новый **MyWatchFaceService.cs** также включает функции для обновления текущего времени каждый раз, когда время зоны изменения (такие как while, проходящих в разных часовых поясах). В конце **MyWatchFaceService.cs**времени изменить зону `BroadcastReceiver` определяется, обрабатывающий изменен часовой пояс намерения объектов: 

```csharp
public class TimeZoneReceiver: BroadcastReceiver
{
    public Action<Intent> Receive { get; set; }
    public override void OnReceive (Context context, Intent intent)
    {
        if (Receive != null)
            Receive (intent);
    }
}
```

`RegisterTimezoneReceiver` И `UnregisterTimezoneReceiver` методы вызываются `OnVisibilityChanged` метод. 
`UnregisterTimezoneReceiver` вызывается при изменении на состояние видимости Циферблат часов скрытый. Когда Циферблат часов не станет снова видимым, `RegisterTimezoneReceiver` вызывается (см. `OnVisibilityChanged` метода). 

Ядро `RegisterTimezoneReceiver` метод объявляет обработчик данный получатель часового пояса `Receive` событий; этот обработчик обновляет `time` объекта с новым значением времени каждый раз, когда пересекаемых часовой пояс: 

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

Блокировка с намерением фильтр создан и зарегистрирован для получателя часовой пояс:

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

`UnregisterTimezoneReceiver` Метод отменяет регистрацию получатель часовой пояс:

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Запустите улучшенные Циферблат часов

Построение и развертывание приложения на устройстве износ еще раз. Выберите Циферблат часов из палитры начертания Контрольные значения как до. Предварительной версии в средстве выбора Контрольное значение отображается в левой части экрана и новый Циферблат часов отображается в правой части:

[![Аналоговый Циферблат часов](creating-a-watchface-images/13-analog-watchface.png "улучшена аналогового начертания в выбора и на устройстве")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

На этом снимке экрана секундная стрелка перемещается один раз в секунду. При выполнении этого кода на устройстве износ секундной стрелки исчезает часами переходит в режим окружения.

 
## <a name="summary"></a>Сводка

В этом пошаговом руководстве пользовательские watchface Android носят был реализованы и протестированы. `CanvasWatchFaceService` И `CanvasWatchFaceService.Engine` появились классы, а основные методы класса ядра были реализованы для создания простого цифровой Циферблат часов. Эта реализация была обновлена с дополнительные функциональные возможности для создания аналогового Циферблат часов, и дополнительные методы были реализованы для обработки изменений в видимость, режим окружения и различия в свойствах устройства. Наконец приемником широковещательных часового пояса было реализовано так, чтобы часами автоматически обновляет время пересечения часового пояса. 


## <a name="related-links"></a>Связанные ссылки

- [Создание гарнитуры Контрольное значение](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Образец WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
