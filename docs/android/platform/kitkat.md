---
title: Функции KitKat
description: Android 4.4 (KitKat) поставляется с изобилия функций для пользователей и разработчиков. В этом руководстве представлены некоторые из этих функций и приводит примеры кода и сведения о реализации, которые помогут наиболее эффективно использовать KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 7b7fa2ea99a58b875bc1dc579455511aa9bf72d1
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172097"
---
# <a name="kitkat-features"></a>Функции KitKat

_Android 4.4 (KitKat) поставляется с изобилия функций для пользователей и разработчиков. В этом руководстве представлены некоторые из этих функций и приводит примеры кода и сведения о реализации, которые помогут наиболее эффективно использовать KitKat._

## <a name="overview"></a>Обзор

Android 4.4 (API уровня 19), также известный как «KitKat», был выпущен в конце 2013. KitKat предлагает широкий набор новых функций и улучшений, включая:

-  [Взаимодействие с пользователем](#user_experience) &ndash; простой анимации с помощью framework перехода, полупрозрачные состояния и панели навигации и полноэкранный режим иммерсивных помогают в создании большее удобство работы пользователя.

-  [Содержимое пользователя](#user_content) &ndash; упрощенное управление файлами пользователя с использованием платформы доступа хранилища; печати изображений, веб-узлы и другое содержимое проще с печатью улучшенные API-интерфейсы.

-  [Оборудование](#hardware) &ndash; преобразуйте любое приложение в NFC карту с помощью NFC узловую эмуляции карты; запустить датчиков низкого энергопотребления с `SensorManager` .

-  [Средства разработчика](#developer_tools) &ndash; презентация приложений в действии с помощью клиента Android Debug Bridge, доступные как часть пакета SDK для Android.


В этом руководстве приводятся инструкции по миграции существующего приложения Xamarin.Android в KitKat, а также общий обзор KitKat для разработчиков Xamarin.Android.

## <a name="requirements"></a>Требования

Для разработки приложений Xamarin.Android, с помощью KitKat, вам потребуется *Xamarin.Android 4.11.0* или более поздней версии и Android 4.4 (API уровня 19) и установить через диспетчер пакетов SDK Android, как показано на следующем снимке экрана:

[![Выбор Android 4.4 в диспетчере пакетов SDK для Android](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Миграция приложения для KitKat

Этот раздел содержит некоторые элементы первого ответа для перехода существующих приложений в Android 4.4.

### <a name="check-system-version"></a>Проверьте версию системы

Если приложению для обеспечения совместимости с предыдущими версиями платформы Android, обязательно заключите любого конкретного KitKat код в проверку версии системы, как показано в следующем примере кода:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Пакетная обработка оповещений

Android использует службы оповещений для пробуждения приложения в фоновом режиме в указанное время. KitKat использует это на шаг дальше, пакетная обработка оповещений для сохранения питания. Это означает, что, вместо пробуждение каждого приложения в определенное время, KitKat, является предпочтительным для группирования нескольких приложений, которые регистрируются для пробуждения во время тот же интервал времени и их пробуждение в то же время.
Чтобы передать в Android пробуждение приложения во время указанного временного интервала, вызовите `SetWindow` на [ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/), передавая минимальное и максимальное время в миллисекундах, по истечении которого пробуждении приложение и операции в выхода из спящего режима.
Ниже приведен пример приложения, который должен быть активирован между получаса и часа с момента окну задан.

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Чтобы продолжить, пробуждение приложения в определенное время, используйте `SetExact`, передавая точного времени, приложение должно быть активирован и операции:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat больше не позволяет задать точное повторяющейся звукового сигнала. Приложения, использующие [`SetRepeating`](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/)
и требует точного сигналы для работы будет теперь нужно вручную запустить каждого сигнала.

### <a name="external-storage"></a>Внешнее хранилище

Внешнее хранилище теперь делится на два типа - хранилища, уникальные для вашего приложения и данных, совместно использоваться несколькими приложениями. Чтение и запись в определенное расположение вашего приложения на внешних носителях, не требует специальных разрешений. Взаимодействие с данными в общем хранилище теперь требует `READ_EXTERNAL_STORAGE` или `WRITE_EXTERNAL_STORAGE` разрешение. Таким образом можно разделить два типа:

-  Если вы получаете путь файла или каталога путем вызова метода на `Context` — например, [`GetExternalFilesDir`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/)
   Или [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - приложению требуются дополнительные разрешения.

-  Если вы получаете путь файла или каталога, доступ к свойству или вызов метода `Environment` , такие как [`GetExternalStorageDirectory`](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/)
   Или [`GetExternalStoragePublicDirectory`](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/)
   , необходимые для приложения `READ_EXTERNAL_STORAGE` или `WRITE_EXTERNAL_STORAGE` разрешение.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` подразумевает `READ_EXTERNAL_STORAGE` разрешение, поэтому следует только когда-нибудь необходимо задать одно разрешение.

### <a name="sms-consolidation"></a>Консолидация SMS

KitKat упрощает обмен сообщениями для пользователя, объединяя все содержимое SMS в приложении одно значение по умолчанию, выбранные пользователем. Разработчик отвечает за делая приложение, можно выбрать, как приложение обмена сообщениями по умолчанию и работает должным образом в коде и в жизни Если приложение не выбран. Дополнительные сведения по переносу приложения SMS для KitKat см [Your SMS приложений Подготовка для KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) руководство от Google.

### <a name="webview-apps"></a>Веб-представление приложений

[Веб-представления](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) есть преобразование KitKat. Самым значительным изменением является Усиленная защита для загрузки содержимого в `WebView`. Хотя большинство приложений, предназначенных для более старых версий API должен работать соответствующим образом, тестирование приложений, использующих `WebView` настоятельно рекомендуется использовать класс. Дополнительные сведения о затронутые API веб-представления см. Android [переход на веб-представления в Android 4.4](http://developer.android.com/guide/webapps/migrating.html) документации.

<a name="user_experience" />

## <a name="user-experience"></a>Взаимодействие с пользователем

KitKat включает ряд новых интерфейсов API, чтобы повысить удобство работы пользователей, включая новую платформу перехода для обработки анимации свойств и прозрачные параметр пользовательского интерфейса для темы. Ниже описаны эти изменения.

### <a name="transition-framework"></a>Framework перехода

Переход framework упрощает реализацию анимации. KitKat позволяет выполнять простое свойство анимации с только одной строкой кода, и настройку с помощью переходов *сцены*.

#### <a name="simple-property-animation"></a>Простое свойство анимации

Новую библиотеку Android переходы упрощает код программной части анимаций свойства. Платформа позволяет выполнять простые анимации с минимальным использованием кода. Например следующий код использует [`TransitionManager.BeginDelayedTransition`](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/)
для анимации отображение и скрытие `TextView`:

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

В примере выше используется платформа перехода для создания автоматического переход по умолчанию между изменение значений свойств. Поскольку анимация обрабатывается с помощью одной строки кода, можно легко вносить это совместим с более старых версиях Android путем заключения `BeginDelayedTransition` вызов в проверку версии системы. См. в разделе [миграции свое приложение к KitKat](#Migrating_Your_App_to_KitKat) раздел для получения дополнительных сведений.

На следующем снимке экрана показано приложение перед анимации:

[![Снимок экрана приложения до запуска анимации](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

На следующем снимке экрана показано приложение после анимации:

[![Снимок экрана приложения после завершения анимации](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Вы можете получить больший контроль над перехода с кадром, которые рассматриваются в следующем разделе.

#### <a name="android-scenes"></a>Android сцены

[Сцены](https://developer.xamarin.com/api/type/Android.Transitions.Scene/) появились в составе framework перехода, чтобы дать разработчику больший контроль над анимации. Автоматически создать динамический область в пользовательском Интерфейсе: Укажите контейнер и несколько версий или «автоматически», для XML-содержимое внутри контейнера, после чего Android выполняет часть работы по анимировать переходы между сценами. Android сцены позволяют создавать сложную анимацию с минимумом усилий со стороны разработчика.

Статический элемент пользовательского интерфейса, размещения динамического содержимого носит название *контейнера* или *сцены базовый*. В следующем примере используется Android Designer для создания `RelativeLayout` вызывается `container`:

[![С помощью Android Designer для создания контейнера RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

Пример макета также определяет кнопка с именем `sceneButton` ниже `container`. Эта кнопка вызовет переход.

Динамическое содержимое внутри контейнера требуется две новых структуры Android. Эти макеты указать только код *внутри* контейнера.
В примере кода ниже определяет макет *Scene1* , содержащий два текстовые поля, чтение, «Пакет» и «Kat» соответственно, а второй макета называются *Scene2* , содержащий те же поля текст, который в обратном порядке. XML-код выглядит следующим образом:

 **Scene1.AXML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.AXML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

В примере выше используется `merge` сократить код представления и упростить просмотр иерархии. Дополнительные сведения о `merge` макеты [здесь](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Сцена создается путем вызова [ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/), передавая объект контейнера, идентификатор ресурса файл макета сцены и текущий `Context`, как показано в следующем примере кода:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

При нажатии кнопки переворачивается между двумя сценами, которые Android анимирует со значениями по умолчанию перехода:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

На следующем снимке экрана показана перед анимации.

[![Снимок экрана приложения до начала анимации](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

На следующем снимке экрана показана сцены после анимации.

[![Снимок экрана приложения после завершения анимации](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> Существует [известная ошибка](https://code.google.com/p/android/issues/detail?id=62450) в Android переходы библиотека, которая вызывает сцены создан с помощью `GetSceneForLayout` будет прерываться, когда пользователь переходит по действия во второй раз. Описывается решение java [здесь](http://www.doubleencore.com/2013/11/new-transitions-framework/).


##### <a name="custom-transitions-in-scenes"></a>Пользовательские переходы в сценах

Пользовательский переход можно определить в файле ресурсов xml в `transition` каталога `Resources`, как показано на снимке экрана ниже:

[![Расположение файла transition.xml в каталоге ресурсов/перехода](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

В следующем образце кода определяет связующее звено, который выполняет анимацию в течение 5 секунд и использует [перенапряжение интерполятора](http://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

Переход создается в действии с помощью [TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/), как показано в следующем коде:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

Затем добавляется новый переход `Go` вызов, который начинается анимация:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Прозрачные пользовательского интерфейса

KitKat обеспечивает больший уровень контроля над темы приложения с помощью необязательно прозрачные состояния и панели навигации. Вы можете изменить полупрозрачность элементов пользовательского интерфейса системы, в том же файле XML, используемого для определения темы Android. KitKat предоставляет следующие свойства:

-  `windowTranslucentStatus` -Если задано значение true, делает строке top состояния прозрачным.

-  `windowTranslucentNavigation` -Если задано значение true, делает нижней панели навигации прозрачным.

-  `fitsSystemWindows` — Задание на панели вверху или внизу transcluent смещает содержимым под прозрачные элементы пользовательского интерфейса по умолчанию. Этому свойству присвоить `true` — простой способ предотвратить перекрытие с элементами интерфейса прозрачные системы содержимое.


Следующий код определяет тему с прозрачным состояния и панели навигации:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

На следующем снимке экрана показан выше темы с прозрачным состояния и панели навигации:

[![Снимок экрана с примером приложения с прозрачным состояния и панели навигации](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Содержимое пользователя

### <a name="storage-access-framework"></a>Framework доступа к хранилищу

Framework доступ хранилища (SAF) — это новый способ пользователям взаимодействовать с хранящиеся данные, например изображения, видео и документы. Вместо диалоговое окно, чтобы выбрать приложение для обработки содержимого, предоставляя пользователям, KitKat открывает новый пользовательский Интерфейс, который позволяет пользователю получить доступ к данным в одном месте статистические. После выбора содержимого, пользователь возвращается к приложению, которое запросило содержимое и взаимодействие с приложениями будут по-прежнему в обычном режиме.

Это изменение требует два действия со стороны разработчика: во-первых, приложения, которым требуются содержимого от поставщиков должны быть обновлены до нового способа запроса содержимого. Во-вторых, приложения, которые записывают данные в `ContentProvider` должны быть изменены для использования новой платформы. Оба сценария зависят от нового [`DocumentsProvider`](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/)
API-ИНТЕРФЕЙС.

#### <a name="documentsprovider"></a>DocumentsProvider

В KitKat, взаимодействие с `ContentProviders` абстрагируются с `DocumentsProvider` класса. Это означает, что SAF безразлично, где данными является физически, пока он доступен через `DocumentsProvider` API. Локальный поставщиков, облачные службы и внешние запоминающие устройства используют интерфейс и обрабатывается так же, предоставляя пользователю и разработчику в одном месте для взаимодействия с пользователем.

В этом разделе описывается, как загружать и сохранять содержимое с помощью платформы доступа хранилища.

#### <a name="request-content-from-a-provider"></a>Содержимое запроса от поставщика

Мы можем сказать KitKat, мы хотим выбрать содержимое с помощью пользовательского интерфейса SAF `ActionOpenDocument` намерения, это означает, что мы хотим подключиться ко всем поставщикам содержимого доступным для устройства. Можно добавить некоторые фильтрацию этого намерения, указав `CategoryOpenable`, означающее только содержимое, которое можно открыть (т. е. доступен, можно использовать содержимое) будет возвращено. KitKat также позволяет фильтровать содержимое с помощью `MimeType`. Например, приведенный ниже код фильтры для результатов образ, указав изображение `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

Вызов `StartActivityForResult` запускает SAF пользовательского интерфейса, в котором пользователь может затем найдите нужный образ:

[![Снимок экрана с примером приложения, используя платформу доступа хранилища для просмотра изображения](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

После пользователь выбрал изображения, `OnActivityResult` возвращает `Android.Net.Uri` выбранного файла. Следующий пример кода отображает выбранный образ пользователя:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>Запись содержимого в поставщик

В дополнение к загрузке содержимого в пользовательском Интерфейсе SAF, KitKat также позволяет сохранить содержимое в любую `ContentProvider` , реализующий `DocumentProvider` API. Сохранение содержимого использует `Intent` с `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

Приведенный выше пример кода загружает SAF пользовательский Интерфейс, уведомляя пользователя, измените имя файла и выберите каталог для размещения нового файла:

[![Снимок экрана, если пользователь изменит имя файла для NewDoc в каталоге файлы для загрузки](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Когда пользователь нажимает **Сохранить**, `OnActivityResult` передается `Android.Net.Uri` вновь созданного файла, к которому можно получить с помощью `data.Data`. Uri может использоваться для потоковой передачи данных в новый файл:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

Обратите внимание, что [`ContentResolver.OpenOutputStream(Android.Net.Uri)`](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri))
Возвращает `System.IO.Stream`, поэтому процесс потоковой передачи можно писать на .NET.

Дополнительные сведения о загрузке, создание и редактирование содержимого с помощью платформы доступа хранилища см [документации для платформы доступа хранилища](http://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Печать

Печать содержимого упрощено в KitKat с появлением [служб печати](https://developer.xamarin.com/api/namespace/Android.PrintServices/) и `PrintManager`. KitKat также — это первая версия API, чтобы в полной мере использовать [API службы облачной печати Google](https://developers.google.com/cloud-print/) с помощью [приложения Google облачной печати](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
Большинство устройств, входящих в состав KitKat автоматически загрузить приложение Google облачной печати и [подключаемый модуль службы печати HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)при первом подключении к сети Wi-Fi. Пользователя можно проверить параметры печати свой устройства, перейдя по адресу **параметры > Система > Печать**:

[![Пример снимка экрана на экране параметров печати](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> Несмотря на то, что API-интерфейсы печати настроены для работы с Google облачной печати по умолчанию, Android по-прежнему позволяет разработчикам Подготовка печати содержимого с помощью новых интерфейсов API и отправить его для других приложений для обработки печати.



#### <a name="printing-html-content"></a>Печати HTML-содержимое

Автоматически создает KitKat [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) для веб-представление с `WebView.CreatePrintDocumentAdapter`. Веб-Печать содержимого является занимаются между [ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) , ожидает на загружаемое содержимое HTML и позволяет действию, чтобы сделать доступным в меню "Параметры" Параметры печати и действие, которое ожидает пользователю Выберите параметры печати и вызовы `Print`на `PrintManager`. В этом разделе рассматриваются основные настройку, необходимую для печати на экране HTML-содержимое.

Обратите внимание, что загрузка и печать веб-содержимого требуется разрешение Internet.

[![Разрешение Internet в параметрах приложения](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Пункт меню печати

Команда «Печать» она обычно будет отображаться в действии [меню параметров](http://developer.android.com/guide/topics/ui/menus.html#options-menu).
Меню «Параметры» позволяет пользователям выполнять действия в действии. Он находится в правом верхнем углу экрана и выглядит следующим образом:

[![Снимок экрана с примером элемента меню печати, отображаемого в правом верхнем углу экрана](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


Можно определить дополнительные пункты меню в *меню*каталога *ресурсы*. Приведенный ниже код определяет пример пункт меню [печати](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

Взаимодействие с меню "Параметры" в действии происходит через `OnCreateOptionsMenu` и `OnOptionsItemSelected` методы.
`OnCreateOptionsMenu` Здесь можно добавить новые элементы меню, таких как вариант «Печать» из *меню* каталог ресурсов.
`OnOptionsItemSelected` ожидает передачи данных для пользователя, выбрав вариант «Печать» в меню и начинается печать:

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

В приведенном выше коде также определена переменная с именем `dataLoaded` для отслеживания состояния HTML-содержимое. `WebViewClient` Будет значение этой переменной значение true, если были загружены все содержимое, поэтому действие знал о необходимости добавления элемента меню печати в меню "Параметры".

##### <a name="webviewclient"></a>WebViewClient

Задача `WebViewClient` необходимо, чтобы данные в `WebView` является полной загрузки, прежде чем команда «Печать» отображается в меню, это делается с помощью `OnPageFinished` метод. `OnPageFinished` ожидает завершения загрузки веб-содержимого, а также указывает действие, чтобы повторно создать параметры меню с `InvalidateOptionsMenu`:

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished` также задает `dataLoaded` значение `true`, поэтому `OnCreateOptionsMenu` можно повторно создать меню с параметром "Печать" на месте.

##### <a name="printmanager"></a>PrintManager

В следующем примере кода выводится содержимое `WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` принимает в качестве аргумента: имя задания печати («MyWebPage» в этом примере), [`PrintDocumentAdapter`](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/)
на основе содержимого, который создает выводимого на печать документа и [`PrintAttributes`](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/)
(`null` в приведенном выше примере). Можно указать `PrintAttributes` для размещения содержимого на странице, несмотря на то, что атрибуты по умолчанию должен обрабатывать большинство сценариев.

Вызов `Print` загружает печати пользовательского интерфейса, в котором перечислены параметры для задания печати. Пользовательский Интерфейс позволяет выполнять печать или сохранение содержимого HTML в PDF-ФАЙЛ, как показано на снимке экрана ниже:

[![Снимок экрана из PrintHtmlActivity отображение меню "Печать"](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Снимок экрана из PrintHtmlActivity отображение сохранения в виде PDF-меню](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Оборудование

KitKat добавляет несколько интерфейсов API для поддержки новых возможностей устройства. Самое значительное из них являются узловую эмуляции карты и новый `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Эмуляции карты на основе узла в NFC

Размещенное карты эмуляции (HCE) позволяет приложениям ведут себя как NFC карт или чтения карт NFC, не полагаясь на собственный элемент транспортной Secure. Перед настройкой HCE, убедитесь, HCE доступен на устройстве с `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

Требует HCE функцию HCE и `Nfc` разрешение регистрироваться с помощью приложения `AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Разрешение NFC в параметрах приложения](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Для работы, HCE должен запускаться в фоновом режиме, и он имеет для запуска, когда пользователь делает операция NFC, даже если приложение, с помощью HCE не выполняется. Это можно сделать путем написания кода HCE как `Service`. Реализует службу HCE `HostApduService` интерфейс, который реализует следующие методы:

-  *ProcessCommandApdu* -An Application протокола данных единицы (APDU) является, какая информация отправляется между чтения NFC и HCE службой. Этот метод использует ADPU из средства чтения и возвращает блок данных в ответе.

-  *OnDeactivated* - `HostAdpuService` отключается, когда служба HCE больше не взаимодействует с устройством чтения NFC.


Служба HCE также должна быть зарегистрировано в манифест приложения и с соответствующими разрешениями, фильтр намерений и метаданных. Следующий код является примером `HostApduService` зарегистрирован с манифестом Android с помощью `Service` атрибут (Дополнительные сведения об атрибутах см. Xamarin [работа с манифестом Android](~/android/platform/android-manifest.md) руководство):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("android.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

Выше службы предоставляет способ чтения NFC для взаимодействия с приложением, но читатель NFC по-прежнему имеет нет способа узнать, что эта служба эмулирующий карточке NFC, требуемые для сканирования. Чтобы помочь идентифицировать службу чтения NFC, можно назначить службе уникальный *идентификатор приложения (AID)*. Мы вспомогательные средства, а также другие метаданные о службе HCE, укажите в файле ресурсов xml зарегистрировано `MetaData` атрибутов (см. приведенный выше код). Этот файл ресурса указывает идентификатор действия один или несколько фильтров — уникальный идентификатор строки в шестнадцатеричном формате, которые соответствуют вспомогательные элементы из одного или нескольких устройств чтения NFC:

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

Помимо фильтров AID к файлу ресурсов xml также содержит описание пользовательского интерфейса службы HCE, определение группы AID (платежном приложении и «other») и в случае с приложением оплаты, 260 x 96 dp баннер для отображения пользователю.

Программа установки, описанные выше предоставляет основные стандартные блоки для приложения, эмуляция карточки NFC. NFC сам требуется несколько дополнительных действий и дополнительное тестирование для настройки. Дополнительные сведения об индивидуальных эмуляции карты, см. [портала документации Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Дополнительные сведения об использовании NFC с Xamarin, ознакомьтесь с [образцы Xamarin NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Датчики

KitKat предоставляет доступ к датчиков устройства через [ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/).
`SensorManager` Позволяет операционной системе расписания доставки данных датчика в приложение в пакетах, сохраняя батареи.

KitKat также поставляется с двумя новыми типами датчика для отслеживания действий пользователя. Они основаны на акселерометр и включают:

-  *StepDetector* -приложение получает уведомление и пробуждении когда пользователь делает шаг, и средства обнаружения предоставляет значение времени для возникновения шаг.

-  *StepCounter* -следит за количество шагов, пользователь перевел, поскольку была зарегистрирована датчика *только после перезагрузки устройства*.

На следующем снимке экрана показан шаг счетчика в действии:

[![Снимок экрана приложения SensorsActivity отображение шаг счетчика](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Можно создать `SensorManager` путем вызова `GetSystemService(SensorService)` и приведения результат в виде `SensorManager`. Чтобы использовать шаг счетчика, вызовите `GetDefaultSensor` на `SensorManager`. Вы можете зарегистрировать датчик и ожидания передачи изменений в число шагов, с помощью [`ISensorEventListener`](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/)
интерфейс, как показано в следующем примере кода:

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

`OnSensorChanged` вызывается, если число на шаге обновления, пока приложение находится на переднем плане. Если приложение входит в фоновом режиме, или устройство находится в спящем режиме, `OnSensorChanged` не будут вызваны; тем не менее, шаги будут продолжать до `UnregisterListener` вызывается.

Имейте в виду, что *значение count шаг накопительного пакета для всех приложений, которые регистрируют датчика*. Это означает, что даже в том случае, если вы удалите и повторно установите приложение и инициализировали `count` переменной со значением 0 при запуске приложения, значений, передаваемых датчиком останется общее число шагов, сделанный во время регистрации датчика, как по вашей приложение или другой. Вы можете запретить приложения от добавления счетчика шаг путем вызова `UnregisterListener` на `SensorManager`, как показано в следующем коде:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Перезагрузка устройства Сбрасывает счетчик шаг 0. Приложение будет требует дополнительного кода, чтобы убедиться, что он отправляет отчеты точное число для приложения, независимо от других приложений, с помощью датчика или состояние устройства.


> [!NOTE]
> Во время API для обнаружения шаг и подсчета поставляется с KitKat не все телефоны будут развернуты с помощью датчика. Можно проверить, доступна ли датчика, запустив `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`, или убедитесь, что возвращаемым значением `GetDefaultSensor` не `null`.


<a name="developer_tools" />

## <a name="developer-tools"></a>Средства разработчика

### <a name="screen-recording"></a>Запись экрана

KitKat включает в себя новый экран, возможности записи, таким образом, разработчики могут записывать приложения в действии. Запись экрана доступна через [Android Debug Bridge (ADB)](http://developer.android.com/tools/help/adb.html) клиент, который можно загрузить как часть пакета SDK для Android.

Для записи экрана, подключение устройства; Затем, найдите установку пакета SDK для Android, перейдите в каталог **средства платформы** каталог и выполните команду **adb** клиента:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

Приведенная выше команда приводит к записи по умолчанию 3-минутное видео с разрешением по умолчанию 4 Мбит/с. Чтобы изменить длину, добавьте *--лимит времени* флаг.
Чтобы изменить разрешение, добавьте *--скорость* флаг. Следующая команда будет записывать видео длительностью в минуту в 8Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Видео можно найти на вашем устройстве — он будет отображаться в коллекции, по завершении записи.


## <a name="other-kitkat-additions"></a>Другие дополнения KitKat

Помимо изменения, описанные выше KitKat позволяет:

-  *Используйте в полноэкранном режиме* -KitKat вводится новый [Иммерсивном режиме](http://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) для просмотра содержимого, игры и выполнения других приложений, которые выиграют от полноэкранного исследования.

-  *Изменить вид уведомлений* -получить дополнительные сведения о системных уведомлений с помощью [`NotificationListenerService`](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/)
   . Это позволяет представить данные по-разному внутри своего приложения.

-  *Зеркальное отображение Прорисовываемые ресурсы* -Прорисовываемые ресурсы имеют новый [`autoMirrored`](http://developer.android.com/reference/android/R.attr.html#autoMirrored)
   атрибут, который сообщает системе, создания зеркальной версии для образов, которые требуют зеркального отражения для макетов слева направо.

-  *Приостановить анимацию* -Приостановка и возобновление анимации, созданные с помощью [`Animator`](https://developer.xamarin.com/api/type/Android.Animation.Animator/)
   .

-  *Чтение динамически изменение текста* -обозначают части пользовательского интерфейса, которые динамически обновить новым текстом «live» регионы с новым [ `accessibilityLiveRegion`](http://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
   атрибут, поэтому новый текст будут считываться автоматически в режим доступности.

-  *Улучшить взаимодействие с аудио* -марки громче отслеживает с [`LoudnessEnhancer`](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/)
   , найти пиковые и RMS аудиопотока с [`Visualizer`](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/)
   класс и получать сведения из [аудио timestamp](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/) для облегчения синхронизации аудио видео.

-  *Синхронизировать ContentResolver интервалом Custom* -KitKat добавляет разницу время, когда выполняется запрос на синхронизацию. Синхронизация `ContentResolver` в настраиваемое время или интервал, вызвав `ContentResolver.RequestSync` и передавая `SyncRequest`.

-  *Различать между контроллерами* -KitKat в контроллеры назначаются уникальные целочисленными идентификаторами, которые можно получить с помощью устройства `ControllerNumber` свойство. Это упрощает нужно сообщить друг от друга игроков в игре.

-  *Удаленное управление* -посредством нескольких изменений в части оборудования и программного обеспечения, KitKat позволяет включать развернуты с помощью передатчика IR в удаленное управление с помощью устройства `ConsumerIrService`, периферийные устройства с новым и взаимодействовать с [`RemoteController`](https://developer.xamarin.com/api/type/Android.Media.RemoteController/)
   API-интерфейсы.

Дополнительные сведения об изменениях API выше, обратитесь к Google [API-интерфейсы Android 4.4](http://developer.android.com/about/versions/android-4.4.html) Обзор.


## <a name="summary"></a>Сводка

В этой статье рассмотрели некоторые новые интерфейсы API, доступные в Android 4.4 (API уровня 19), а также рассматриваются рекомендации при переходе приложения к KitKat. ИТ возникают контурные изменения в API-интерфейсы влияния на пользователей, включая *перехода framework* и новые параметры для *темы*. Затем она введена *Framework доступа к хранилищу* и `DocumentsProvider` класса, а также новый *API-интерфейсы печати*. Он изучили *эмуляции карты на основе узла NFC* и способы работы с *датчиков низкого энергопотребления*, включая две новые датчики для отслеживания действий пользователя. Наконец, демонстрируется захват в режиме реального времени демонстрации приложений с *запись экрана*и предоставлен подробный список KitKat API изменения и дополнения.


## <a name="related-links"></a>Связанные ссылки

- [Пример KitKat](https://developer.xamarin.com/samples/KitKat/)
- [Android 4.4 API-интерфейсов](http://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](http://developer.android.com/about/versions/kitkat.html)
