---
title: Создание оформлений экрана для Android Wear 1.0
description: В этом руководстве объясняется, как реализовать службу пользовательские Контрольные значения лиц для Android Wear 1.0. Пошаговые инструкции для создания является урезанным работу службы цифровых watch лиц, следуют дополнительные код, чтобы создать стиль аналоговый циферблате.
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/23/2018
ms.openlocfilehash: f46e0d27a39c7734e63bf5603ef2e47cd5fa7aa9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105536"
---
# <a name="creating-a-watch-face"></a>Создание оформлений экрана

_В этом руководстве объясняется, как реализовать службу пользовательские Контрольные значения лиц для Android Wear 1.0. Пошаговые инструкции для создания является урезанным работу службы цифровых watch лиц, следуют дополнительные код, чтобы создать стиль аналоговый циферблате._

## <a name="overview"></a>Обзор

В этом пошаговом руководстве Чтобы проиллюстрировать основы создания пользовательских оформлений экрана Android Wear 1.0 создается услуга лиц основные контрольные значения.
Служба распознавания лиц начальное Контрольное значение отображает простой цифровой Контрольные значения, отображается текущее время в часах и минутах:

[![Цифровой циферблате](creating-a-watchface-images/01-initial-face.png "пример снимка экрана начальной цифровой циферблате")](creating-a-watchface-images/01-initial-face.png#lightbox)

После этого цифровой циферблате разрабатывается и протестированы, дополнительные код добавляется до более сложных аналогового циферблате с тремя руки:

[![Аналоговые циферблате](creating-a-watchface-images/02-example-watchface.png "пример снимка экрана на окончательный аналогового циферблате")](creating-a-watchface-images/02-example-watchface.png#lightbox)

Службы лиц Watch объединено и устанавливается как часть приложения Wear 1.0. В следующих примерах `MainActivity` содержит не более чем код из шаблона приложения Wear 1.0, чтобы служба лиц Контрольные значения можно упаковать и развернут умные часы в виде часть приложения. По сути это приложение будет использоваться исключительно как автомобиля для получения службе лиц watch, загруженной в устройстве Wear 1.0 (или эмуляторе) для отладки и тестирования.

## <a name="requirements"></a>Требования

Реализация службы watch лиц, вот требуется:

-   Android 5.0 (уровень API 21) или более поздней версии на устройстве Android Wear или эмуляторе.

-   [Библиотек поддержки Android Wear Xamarin](https://www.nuget.org/packages/Xamarin.Android.Wear) необходимо добавить в проект Xamarin.Android.

Несмотря на то, что Android 5.0 минимальное API уровня для реализации службы лиц watch, Android 5.1 или более поздней версии рекомендуется использовать. Android Wear устройств под управлением Android 5.1 (API 22) или более поздней версии разрешить износа приложения для управления, что отображается на экране, пока устройство находится в энергосберегающее *окружения* режим. При удалении устройства из низкого энергопотребления *окружения* режиме, он находится в *интерактивные* режим. Дополнительные сведения об этих режимах см. в разделе [сохранение свое приложение видимым](https://developer.android.com/training/wearables/apps/always-on.html).


## <a name="start-an-app-project"></a>Запуск проекта приложения

Создайте новый проект Android Wear 1.0 с именем **WatchFace** (Дополнительные сведения о создании новых проектов Xamarin.Android см. в разделе [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Диалоговое окно нового проекта](creating-a-watchface-images/03-wear-project-vs-sml.png "выберите приложение Wear в диалоговом окне нового проекта")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Диалоговое окно нового проекта](creating-a-watchface-images/03-wear-project-xs-sml.png "выберите приложение Wear в диалоговом окне нового проекта")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


Задайте имя пакета `com.xamarin.watchface`:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Параметр имени пакета](creating-a-watchface-images/04-package-name-vs.png "присвоено имя пакета com.xamarin.watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Параметр имени пакета](creating-a-watchface-images/04-package-name-xs.png "присвоено имя пакета com.xamarin.watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Кроме того, прокрутите вниз и включите **INTERNET** и **WAKE_LOCK** разрешения:

[![Необходимые разрешения](creating-a-watchface-images/05-required-permissions-vs.png "разрешить и WAKE_LOCK разрешения")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Значение версии Android минимум **Android 5.1 (уровень API 22)**.
Кроме того, включение **Internet** и **WakeLock** разрешения:

[![Необходимые разрешения](creating-a-watchface-images/05-required-permissions-xs.png "разрешить и WakeLock разрешения")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

Теперь скачайте [preview.png](creating-a-watchface-images/preview.png) &ndash; добавляется к **прорисовываемых ресурсов** папку позже в этом пошаговом руководстве.


## <a name="add-the-xamarinandroid-wear-package"></a>Добавьте пакет износа Xamarin.Android

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Запустите диспетчер пакетов NuGet (в Visual Studio щелкните правой кнопкой мыши **ссылки** в **обозревателе решений** и выберите **управление пакетами NuGet...** ). Обновить проект до последней стабильной версии **Xamarin.Android.Wear**:

[![Диспетчер пакетов NuGet добавьте](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "добавить пакет Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

Далее, если **Xamarin.Android.Support.v13** будет установлено, удалите его:

[![Удалить диспетчер пакетов NuGet](creating-a-watchface-images/07-uninstall-v13-sml.png "удалить Xamarin.Support.v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Запустите диспетчер пакетов NuGet (в Visual Studio для Mac, щелкните правой кнопкой мыши **пакетов** в **область решений** и выберите **добавить пакеты...** ). Обновить проект до последней стабильной версии **Xamarin.Android.Wear**:

[![Диспетчер пакетов NuGet добавьте](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "добавить пакет Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


Сборка и запуск приложения на устройстве Android Wear или в эмуляторе (Дополнительные сведения о том, как это сделать, см. в разделе [Приступая к работе](~/android/wear/get-started/index.md) руководстве). Вы должны увидеть на следующем экране приложения на устройстве Android Wear:

[![Снимок экрана приложения](creating-a-watchface-images/08-app-screen.png "экран приложения на устройстве Android Wear")](creating-a-watchface-images/08-app-screen.png#lightbox)

На этом этапе базовое приложение Wear имеет функциональных возможностей распознавания лиц Контрольные значения, так как он еще не обеспечивает реализацию службы лиц Контрольные значения. Эта служба будет добавляться рядом.


## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android Wear реализует смотреть лиц с помощью `CanvasWatchFaceService` класса. `CanvasWatchFaceService` является производным от `WatchFaceService`, который сам является производным от `WallpaperService` как показано на следующей схеме:

[![Схема наследования](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService диаграмма наследования")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` включает в себя вложенный `CanvasWatchFaceService.Engine`; он создает экземпляр `CanvasWatchFaceService.Engine` объект, который выполняет фактическую работу рисования на циферблате. `CanvasWatchFaceService.Engine` является производным от `WallpaperService.Engine` как показано на схеме выше.

Не показано на этой схеме является `Canvas` , `CanvasWatchFaceService` используется для рисования на циферблате &ndash; это `Canvas` переданной через `OnDraw` метод, как описано ниже.

В следующих разделах будут созданы службы лиц пользовательские Контрольные значения, сделав следующее:

1.  Определите класс с именем `MyWatchFaceService` , производный от `CanvasWatchFaceService`.

2.  В рамках `MyWatchFaceService`, создать вложенный класс с именем `MyWatchFaceEngine` , производный от `CanvasWatchFaceService.Engine`.

3.  В `MyWatchFaceService`, реализовать `CreateEngine` метод, создающий экземпляр `MyWatchFaceEngine` и возвращает его.

4.  В `MyWatchFaceEngine`, реализовать `OnCreate` метод для создания стиля грани контрольных значений и выполнения других задач инициализации.

5.  Реализуйте `OnDraw` метод `MyWatchFaceEngine`. Этот метод вызывается всякий раз, когда на циферблате должна быть перерисована (т. е. *недействительным*). `OnDraw` — метод, который рисует (и перерисовывает) элементы лиц Контрольные значения, такие как час, минуту и второй руки.

6.  Реализуйте `OnTimeTick` метод `MyWatchFaceEngine`.
    `OnTimeTick` вызывается по крайней мере один раз в минуту (в окружающей среды и интерактивный режим) или при изменении даты и времени.

Дополнительные сведения о `CanvasWatchFaceService`, см. в разделе Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) документации по API.
Аналогичным образом [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) объясняет фактическую реализацию на циферблате.


### <a name="add-the-canvaswatchfaceservice"></a>Добавить CanvasWatchFaceService

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Добавьте новый файл с именем **MyWatchFaceService.cs** (в Visual Studio щелкните правой кнопкой мыши **WatchFace** в **обозревателе решений**, нажмите кнопку **Добавить > новый элемент...** и выберите **класс**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Добавьте новый файл с именем **MyWatchFaceService.cs** (в Visual Studio для Mac, щелкните правой кнопкой мыши **WatchFace** проекта, щелкните **Добавить > новый файл...** и выберите **пустой класс**).

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

`MyWatchFaceService` (производный от `CanvasWatchFaceService`) — это «основная программа» на циферблате. `MyWatchFaceService` реализует только один метод `OnCreateEngine`, который создает и возвращает `MyWatchFaceEngine` объекта (`MyWatchFaceEngine` является производным от `CanvasWatchFaceService.Engine`). Созданный экземпляр `MyWatchFaceEngine` объектом, возвращаемым как `WallpaperService.Engine`. Инкапсуляция `MyWatchFaceService` объект передается в конструктор.

`MyWatchFaceEngine` — Это реализация лиц фактическое watch &ndash; он содержит код, который рисует на циферблате. Он также обрабатывает системные события, такие как изменения экрана (окружения/интерактивного режима экрана, отключение и т. д.).


### <a name="implement-the-engine-oncreate-method"></a>Реализуйте метод OnCreate ядра

`OnCreate` Метод инициализирует на циферблате. Добавьте следующее поле `MyWatchFaceEngine`:

```csharp
Paint hoursPaint;
```

Это `Paint` объекта будет использоваться для рисования на циферблате текущее время. Добавьте следующий метод в `MyWatchFaceEngine`:

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

`OnCreate` вызывается вскоре после `MyWatchFaceEngine` запущена. Она настраивает `WatchFaceStyle` (контроль как износа устройство взаимодействует с пользователем) и создает экземпляр `Paint` объект, который будет использоваться для отображения времени.

Вызов `SetWatchFaceStyle` делает следующее:

1.  Наборы *режим просмотра* для `PeekModeShort`, чего уведомления в виде карточек небольшой «Показать» на экране.

2.  Задает видимость фона `Interruptive`, чего фон карты обзора будет отображаться только на короткое время если оно представляет уведомление прерывающие работу.

3.  Отключает системное время пользовательского интерфейса по умолчанию из изображаемого циферблате таким образом, чтобы пользовательские циферблате вместо отображения времени.

Дополнительные сведения об этих и других параметров стиля лиц Контрольные значения, см. в разделе Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) документации по API.

После `SetWatchFaceStyle` завершения `OnCreate` создает `Paint` объекта (`hoursPaint`) и задает ее цвет белым цветом и его размер шрифта до 48 пикселей ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) должен быть указан в пикселях).


### <a name="implement-the-engine-ondraw-method"></a>Реализуйте метод OnDraw ядра

`OnDraw` Метод является наиболее важным, возможно `CanvasWatchFaceService.Engine` метод &ndash; этот метод, что фактически рисует смотрите лиц элементы, такие как цифры и clock руки лиц.
В следующем примере он рисует строку времени на циферблате.
Добавьте следующий метод в `MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str,
        (float)(frame.Left + 70),
        (float)(frame.Top  + 80), hoursPaint);
}
```

При вызове метода Android `OnDraw`, он передает в `Canvas` экземпляра и границы, в которых можно рисовать начертание шрифта. В приведенном выше примере кода `DateTime` используется для вычисления текущего времени в часах и минутах (в 12-часовом формате). Результирующая строка времени рисуется на холсте с помощью `Canvas.DrawText` метод. Строка отобразится 70 точек отказа от левого края и 80 пикселей вниз относительно верхнего края.

Дополнительные сведения о `OnDraw` метод, см. в разделе Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) документации по API.


### <a name="implement-the-engine-ontimetick-method"></a>Реализуйте метод OnTimeTick ядра

Android периодически вызывает `OnTimeTick` метод обновления времени, отображаемого на циферблате. Он называется в как минимум один раз в минуту (в окружающей среды и интерактивный режим), или при изменении даты и времени или часового пояса. Добавьте следующий метод в `MyWatchFaceEngine`:

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Эта реализация `OnTimeTick` просто вызывает `Invalidate`. `Invalidate` Расписания метод `OnDraw` перерисовывает на циферблате.

Дополнительные сведения о `OnTimeTick` метод, см. в разделе Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) документации по API.


## <a name="register-the-canvaswatchfaceservice"></a>Зарегистрировать CanvasWatchFaceService

`MyWatchFaceService` должен быть зарегистрирован в **AndroidManifest.xml** из связанного приложения одежды. Чтобы сделать это, добавьте следующий XML-код для `<application>` раздел:

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

Этот XML-код выполняет следующие функции:

1.  Наборы `android.permission.BIND_WALLPAPER` разрешение. Это разрешение дает watch лиц службы разрешение на изменение обоев системы на устройстве. Обратите внимание, что это разрешение должно быть задано в `<service>` раздел, а не во внешнем `<application>` раздел.

2.  Определяет `watch_face` ресурсов. Этот ресурс находится в короткое XML-файл, который объявляет `wallpaper` ресурсов (этот файл будет создан в следующем разделе).

3.  Объявляет drawable образ с именем `preview` , будет отображаться на экране выбора средства выбора контрольных значений.

4.  Включает в себя `intent-filter` для Android следует помнить, что `MyWatchFaceSevice` будут смотреть оформлений.

На этом завершается создание кода для базовой `WatchFace` пример. Следующим шагом является добавление необходимых ресурсов.


## <a name="add-resource-files"></a>Добавить файлы ресурсов

Перед запуском службы Контрольные значения, необходимо добавить **watch_face** ресурсов и изображение для предварительного просмотра. Во-первых, создайте новый файл XML в **Resources/xml/watch_face.xml** и замените его содержимое следующим кодом XML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Задайте действие сборки этот файл для **AndroidResource**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Действие при сборке](creating-a-watchface-images/10-android-resource-vs.png "задание сборки действие AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Действие при сборке](creating-a-watchface-images/10-android-resource-xs.png "задание сборки действие AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Этот файл ресурсов определяет простой `wallpaper` элемент, который будет использоваться для на циферблате.

Если еще не сделано, загрузите [preview.png](creating-a-watchface-images/preview.png).
Установить по этому **Resources/drawable/preview.png**. Не забудьте добавить этот файл, чтобы `WatchFace` проекта. Изображение для предварительного просмотра отображается для пользователей в средстве выбора лиц watch на устройстве Android Wear. Создание эскиза для собственного грани Контрольные значения, можно делать снимки экрана на циферблате, пока оно выполняется. (Дополнительные сведения о получении снимков экрана с износа устройств, см. в разделе [делать снимки экрана](~/android/wear/deploy-test/debug-on-device.md#screenshots)).


## <a name="try-it"></a>Попробуйте!

Создавайте и развертывайте приложения на устройстве Android Wear. Вы должны увидеть экран приложения износа отображаются как и раньше. Выполните следующую команду, чтобы включить новое лицо контрольных значений.

1.  Проведите по экрану вправо, пока не появится фон окна контрольных значений.

2.  Touch и в любом месте хранения на заднем плане экрана на две секунды.

3.  Проведите по экрану от левого края справа, чтобы просмотреть различные фрагменты контрольных значений.

4.  Выберите **пример Xamarin** смотреть лиц (справа):

    [![Средство выбора Watchface](creating-a-watchface-images/11-watchface-picker.png "проведите по экрану, чтобы найти пример Xamarin циферблате")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  Коснитесь **пример Xamarin** смотреть лиц, чтобы выбрать его.

При этом изменяется на циферблате износа устройства, чтобы использовать службы лиц пользовательские Контрольные значения, реализованной в данный момент:

[![Цифровой циферблате](creating-a-watchface-images/12-digital-watchface.png "цифровых Контрольное значение Custom на устройстве Android Wear")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Это относительно сырой оформлений, поскольку таким образом минимальный реализации приложения (например, она не включает фона лицевой Контрольные значения и не вызывает `Paint` сглаживание методы, чтобы улучшить внешний).
Тем не менее он реализует Минималистическая функциональные возможности, необходимые для создания пользовательских оформлений.

В следующем разделе это циферблате обновляется до более сложные реализации.


## <a name="upgrading-the-watch-face"></a>Обновление на циферблате

В оставшейся части этого пошагового руководства `MyWatchFaceService` обновляется для отображения циферблате стиле аналог для языка и расширяется для поддержки дополнительных функций. Для создания обновленной циферблате будут добавлены следующие возможности:

1.  Указывает время аналогового час, минуту и второй руки.

2.  Реагирует на изменения видимости.

3.  Реагирует на изменения между внешней и интерактивный режим.

4.  Считывает свойства в базовое устройство одежды.

5.  Автоматически обновляет время, когда происходит изменение часового пояса.

Перед реализацией изменений в код ниже, скачайте [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), распакуйте его и перемещение распакованный PNG-файлы для **ресурсы/drawable** (заменяет предыдущую **preview.png**). Добавить новый PNG-файлы для `WatchFace` проекта.


### <a name="update-engine-features"></a>Функции обновления ядра

Следующим шагом является обновление **MyWatchFaceService.cs** на реализацию, который рисует аналогового циферблате и поддерживает новые функции. Замените содержимое файла **MyWatchFaceService.cs** с аналогового версию кода лиц Контрольные значения в [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (можно вырезать и вставить этот источник в существующую  **MyWatchFaceService.cs**).

Эта версия **MyWatchFaceService.cs** добавляет дополнительный код в существующие методы и включает дополнительные переопределенные методы, расширяющие функциональные возможности. Следующие разделы содержат обзор возможностей исходный код.

#### <a name="oncreate"></a>OnCreate

Обновленный **OnCreate** метод задает стиль лиц Контрольные значения, как и раньше, но она также включает некоторые дополнительные действия:

1.  Задает фоновое изображение **xamarin_background** ресурс, который находится в **Resources/drawable-hdpi/xamarin_background.png**.

2.  Инициализирует `Paint` объектов для рисования часовой стрелки, минутной и секундной стрелки.

3.  Инициализирует `Paint` для рисования делений час по краю на циферблате.

4.  Создает таймер, который вызывает `Invalidate` метод (перерисовки), чтобы секундной стрелки перерисовывается каждую секунду. Обратите внимание, что этот таймер необходима потому что `OnTimeTick` вызовы `Invalidate` только один раз каждую минуту.

В этом примере включает в себя только один **xamarin_background.png** образ; тем не менее, вы можете создать другой фоновое изображение для каждого экрана плотности, с поддержкой пользовательского циферблат.

#### <a name="ondraw"></a>OnDraw

Обновленный **OnDraw** метод рисует стиле аналоговый циферблате, выполнив следующие действия:

1.  Получает текущее время, которое теперь хранится в `time` объекта.

2.  Определяет границы области рисования и его центра.

3.  Рисует фон, масштабируется по размерам устройства при рисовании фона.

4.  Рисует двенадцати *тактов* вокруг циферблата часов (соответствующий на Циферблат часов).

5.  Вычисляет угол, поворота и длина каждой стрелки контрольных значений.

6.  Рисует каждой стрелки в рабочей области контрольных значений. Обратите внимание, что секундной стрелки не выводится, если часы находятся в режиме окружения.


#### <a name="onpropertieschanged"></a>OnPropertiesChanged

Этот метод вызывается для информирования `MyWatchFaceEngine` о свойствах устройства Wear (например, "low разрядном режиме окружающей среды и тестирования защиты). В `MyWatchFaceEngine`, этот метод только проверяет наличие низким разрядном режиме окружающей среды (в режиме низкой окружения экрана поддерживает меньшее число битов для каждого цвета).

Дополнительные сведения об этом методе см. в разделе Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) документации по API.


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Этот метод вызывается, когда устройстве Android Wear входит или выходит из режима окружения. В `MyWatchFaceEngine` реализации на циферблате отключает сглаживания, когда он находится в режиме окружения.

Дополнительные сведения об этом методе см. в разделе Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) документации по API.


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

Этот метод вызывается всякий раз, когда часы становится видимым или скрытым. В `MyWatchFaceEngine`, этот метод регистрирует и отменяет регистрацию получатель часового пояса (описаны ниже) в зависимости от состояния видимости.

Дополнительные сведения об этом методе см. в разделе Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) документации по API.


### <a name="time-zone-feature"></a>Компонент часового пояса

Новый **MyWatchFaceService.cs** также предусмотрена возможность обновить текущее время каждый раз, когда часовых поясов изменения (например, во время поездок в разных часовых поясах). В конце **MyWatchFaceService.cs**времени изменить зоны `BroadcastReceiver` определяется, обрабатывающий изменения часового пояса объекты целей:

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

`RegisterTimezoneReceiver` И `UnregisterTimezoneReceiver` методы вызываются службами `OnVisibilityChanged` метод.
`UnregisterTimezoneReceiver` вызывается при изменении состояния видимости на циферблате для скрытых. На циферблате отображается еще раз, когда `RegisterTimezoneReceiver` вызывается (см. в разделе `OnVisibilityChanged` метод).

Ядро `RegisterTimezoneReceiver` метод объявляет обработчик данный получатель часовой пояс `Receive` задание; этот обработчик обновляет `time` объекта с новым значением времени каждый раз, когда пересечения часовой пояс:

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

Фильтром намерений создается и регистрируется для получателя часовой пояс:

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

`UnregisterTimezoneReceiver` Метод отменяет регистрацию получатель часовой пояс:

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Запустите улучшенные циферблате

Создавайте и развертывайте приложения на устройстве Android Wear еще раз. Выберите на циферблате из палитры лиц контрольных значений как перед. Предварительной версии в средстве выбора Контрольное значение отображается слева и справа отображается новое лицо Контрольные значения:

[![Аналоговые циферблате](creating-a-watchface-images/13-analog-watchface.png "Улучшенная аналогового лиц в средстве выбора и на устройстве")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

На этом снимке экрана секундной стрелки — это перенос один раз в секунду. При выполнении этого кода на устройстве Android Wear секундной стрелки исчезает, когда часы переходит в режим окружения.


## <a name="summary"></a>Сводка

В этом пошаговом руководстве пользовательские watchface Android Wear 1.0 был реализации и тестирования. `CanvasWatchFaceService` И `CanvasWatchFaceService.Engine` появились классы, и основные методы класса ядра были реализованы для создания простого цифровой оформлений. Эта реализация была обновлена с дополнительные функциональные возможности для создания аналогового циферблате и дополнительные методы были реализованы для обработки изменений в видимость окружения режим и различия в свойствах устройства. И, наконец часовой пояс широковещательный приемник был реализован таким образом, чтобы часы автоматически обновляет время, когда пересечения часового пояса.


## <a name="related-links"></a>Связанные ссылки

- [Создание контрольных значений лиц](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Пример WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
