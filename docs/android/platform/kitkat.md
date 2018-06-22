---
title: Функции KitKat
description: Android 4.4 (KitKat) загружена изобилия возможности для пользователей и разработчиков. В этом руководстве представлены некоторые из этих функций и приводит примеры кода и сведения о реализации, которые помогут наиболее эффективно использовать KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 3c3eafc8dc18113080dd6c906025556292c43e1c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773742"
---
# <a name="kitkat-features"></a>Функции KitKat

_Android 4.4 (KitKat) загружена изобилия возможности для пользователей и разработчиков. В этом руководстве представлены некоторые из этих функций и приводит примеры кода и сведения о реализации, которые помогут наиболее эффективно использовать KitKat._

## <a name="overview"></a>Обзор

Android 4.4 (API уровня 19), также известные как «KitKat», был выпущен в конце 2013. KitKat предоставляет множество новых функций и улучшений, включая:

-  [Взаимодействие с пользователем](#user_experience) &ndash; легко анимации с framework перехода, полупрозрачные состояния и панели навигации и полноэкранном режиме впечатляющие помогают создавать для повышения удобства для пользователя.

-  [Содержимое пользователя](#user_content) &ndash; упрощенное управление файлами пользователя с framework доступа хранилища; печати изображений, веб-узлы и другое содержимое легче, улучшенная печати API-интерфейсы.

-  [Оборудование](#hardware) &ndash; включить любое приложение NFC карту с NFC размещенное эмуляции карт; запуска маломощных датчики с `SensorManager` .

-  [Инструменты разработчика](#developer_tools) &ndash; видеоролике приложений в действие для клиента мост отладки Android, доступные в составе пакета SDK для Android.


В этом руководстве приводятся инструкции по миграции существующего приложения Xamarin.Android KitKat, а также высокоуровневый обзор KitKat Xamarin.Android разработчикам.

## <a name="requirements"></a>Требования

Для разработки приложений Xamarin.Android, с помощью KitKat, необходимо *Xamarin.Android 4.11.0* или более поздней версии и Android 4.4 (API уровня 19) и установить через диспетчер Android SDK, как показано на следующем снимке экрана:

[![При выборе Android 4.4 в диспетчере Android SDK](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Миграция приложения для KitKat

Этот раздел содержит некоторые элементы первого ответа для перехода на существующие приложения на Android 4.4.

### <a name="check-system-version"></a>Проверьте версию системы

Если приложению требуется для обеспечения совместимости с более ранними версиями Android, не забудьте заключить код конкретного KitKat в проверку версии системы, как показано в следующем примере:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Пакетная обработка сигналов

Android использует службы звуковых сигналов для пробуждения приложения в фоновом режиме в указанное время. KitKat принимает это дальше по пакетной обработки сигналов для сохранения питания. Это означает, что вместо пробуждение каждое приложение в определенное время, KitKat является предпочтительным для группирования нескольких приложений, которые регистрируются для пробуждения же период времени и пробуждения их в то же время.
Чтобы сообщить Android, чтобы пробудить приложения за определенный промежуток времени, вызовите `SetWindow` на [ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/), передавая минимальное и максимальное время в миллисекундах, которое может пройти до пробуждении приложения и операции на выход из спящего режима.
Следующий код содержит пример приложения, который должен переводить в рабочий режим между полчаса и часа с момента окно установлено:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Чтобы продолжить, пробуждение приложения в определенное время, используйте `SetExact`, передавая точное время приложения следует переводить в рабочий режим и операцию для выполнения:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat больше не позволяет задать точное повторяющейся звукового сигнала. Приложения, использующие [ `SetRepeating` ](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/) и Требовать точного сигналы для работы будет необходимо вручную активировать каждого сигнала.

### <a name="external-storage"></a>Внешнее хранилище

Внешнее хранилище теперь состоит из двух типов — хранилище, уникальные для приложений, а также данных, совместно используется несколькими приложениями. Чтение и запись в указанное местоположение приложения на внешних носителях, не требует специальных разрешений. Требует взаимодействия с данными в общем хранилище теперь `READ_EXTERNAL_STORAGE` или `WRITE_EXTERNAL_STORAGE` разрешение. Таким образом можно разделить два типа:

-  Если требуется получить пути файла или каталога путем вызова метода для `Context` — например, [ `GetExternalFilesDir` ](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/) или [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - вашему приложению требуется никаких дополнительных разрешений.

-  Если вы видите путь файла или каталога, доступ к свойству или вызов метода `Environment` , такие как [ `GetExternalStorageDirectory` ](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/) или [ `GetExternalStoragePublicDirectory` ](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/) , вашему приложению требуется `READ_EXTERNAL_STORAGE` или `WRITE_EXTERNAL_STORAGE` разрешение.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` подразумевает `READ_EXTERNAL_STORAGE` разрешение, поэтому следует только когда-либо необходимо задать одно разрешение.

### <a name="sms-consolidation"></a>Консолидация SMS

KitKat упрощает обмена сообщениями для пользователя, применяя статистическую обработку все содержимое SMS в приложении одно значение по умолчанию, выбранные пользователем. Разработчик отвечает за делая приложение доступным для выбора приложения обмена сообщениями по умолчанию и работает должным образом в коде и в жизни Если приложение не установлено. Дополнительные сведения о переходе приложению SMS KitKat посвящены [Your SMS приложений Подготовка для KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) из Google руководства по.

### <a name="webview-apps"></a>Веб-представление приложений

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) есть преобразование в KitKat. Наиболее значительное изменение добавляется безопасности для загрузки содержимого в `WebView`. Хотя большинство приложений, предназначенных для более старых версий API должен работать соответствующим образом, тестирование приложений, использующих `WebView` настоятельно рекомендуется использовать класс. Дополнительные сведения об API-интерфейсах затронутых WebView посвящены Android [переход на WebView в Android 4.4](http://developer.android.com/guide/webapps/migrating.html) документации.

<a name="user_experience" />

## <a name="user-experience"></a>Взаимодействие с пользователем

KitKat включает ряд новых интерфейсов API, чтобы улучшить взаимодействие с пользователем, включая новую платформу перехода для обработки свойства анимации и полупрозрачных параметр пользовательского интерфейса для использования тем. Эти изменения рассматриваются ниже.

### <a name="transition-framework"></a>Переход Framework

Переход framework упрощает реализацию анимации. KitKat позволяет выполнять простое свойство анимации с только одной строки кода, и настройку с помощью переходов *сцен*.

#### <a name="simple-property-animation"></a>Простое свойство анимации

Новую библиотеку Android переходы упрощает код, стоящий за анимации свойства. Платформа дает возможность выполнять простой анимации с помощью минимального программного кода. Например, следующий код использует образец [ `TransitionManager.BeginDelayedTransition` ](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/) для анимации отображение и скрытие `TextView`:

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

В приведенном выше примере перехода используется для создания автоматического перехода по умолчанию между изменение значений свойств. Так как анимация обрабатывается с помощью одной строки кода, можно легко вносить это совместимы с прежними версиями Android путем заключения `BeginDelayedTransition` вызов в проверки версии системы. В разделе [миграции вашего приложения для KitKat](#Migrating_Your_App_to_KitKat) раздел для получения дополнительных сведений.

На снимке экрана ниже показано приложение, прежде чем анимации:

[![Снимок экрана приложения до начала анимации](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

На снимке экрана ниже показано приложение после анимации:

[![Снимок экрана приложения после завершения анимации](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Вы можете получить больший контроль над перехода с сцен, которые рассматриваются в следующем разделе.

#### <a name="android-scenes"></a>Android сцены

[Сцены](https://developer.xamarin.com/api/type/Android.Transitions.Scene/) впервые появились в составе платформе переход, чтобы дать разработчику больший контроль над анимации. Автоматически создать динамический область в пользовательском Интерфейсе: Укажите контейнер и несколько версий или «автоматически», для XML-содержимое внутри контейнера, и делает все остальное работы анимировать переходы между автоматически Android. Android сцен, позволяют создавать сложные анимации с минимальными трудозатратами на стороне разработки.

Статический элемент пользовательского интерфейса, где размещены динамическое содержимое, называется *контейнера* или *сцены базовый*. В следующем примере используется конструктор Android для создания `RelativeLayout` вызывается `container`:

[![С помощью конструктора Android для создания контейнера RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

Макет образец также определяет кнопку с именем `sceneButton` ниже `container`. Эта кнопка будет вызывать переход.

Динамическое содержимое внутри контейнера требуются две новые макеты Android. Эти макеты указать только код *внутри* контейнера.
В примере кода ниже определяет макет *Scene1* , содержащий два текстовые поля, чтение «Пакет» и «Kat» соответственно, а второй макета называются *Scene2* , содержит те же поля текста, в обратном направлении. Код XML выглядит следующим образом:

 **Scene1.axml**:

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

 **Scene2.axml**:

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

Это может быть создается путем вызова [ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/), передавая объект-контейнер, идентификатор ресурса сцены файл макета и текущий `Context`, как показано в следующем примере:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Нажав кнопку зеркальное отражение между двумя сцен, которых Android анимирует переход значениями по умолчанию:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

На следующем снимке экрана показано перед анимации.

[![Снимок приложения до начала анимации](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

На следующем снимке экрана показано сцены после анимации.

[![Снимок экрана приложения после завершения анимации](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> Отсутствует [известная ошибка](https://code.google.com/p/android/issues/detail?id=62450) в Android переходы библиотека, которая вызывает сцен создан с помощью `GetSceneForLayout` будет прерываться, когда пользователь переходит к действие еще раз. Описывается решение java [здесь](http://www.doubleencore.com/2013/11/new-transitions-framework/).


##### <a name="custom-transitions-in-scenes"></a>Пользовательские переходов в сцены

Можно определить пользовательский переход в файл ресурсов xml в `transition` каталог в разделе `Resources`, как показано на снимке экрана ниже:

[![Расположение файла transition.xml в каталоге ресурсов и перехода](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

В следующем примере кода определяется переход, который выполняет анимацию в течение 5 секунд и использует [перенапряжение интерполятора](http://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

Переход в действие создается с помощью [TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/), как показано в следующем примере кода:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

Затем добавляется новый переход `Go` вызов, который начинает анимации:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Прозрачная пользовательского интерфейса

KitKat дает больше контроля над тем приложения с необязательным transclucent состояния и панели навигации. Вы можете изменять прозрачность элементов пользовательского интерфейса системы, в том же файле XML, используемого для определения темы Android. KitKat предоставляет следующие свойства:

-  `windowTranslucentStatus` -При установке в значение true, делает строки верхнего состояния полупрозрачные.

-  `windowTranslucentNavigation` -Если задано значение true, делает нижней панели навигации полупрозрачные.

-  `fitsSystemWindows` -Параметр в верхней или нижней строке transcluent сдвигает содержимое в прозрачные элементы пользовательского интерфейса по умолчанию. Присвоение этому свойству `true` — простой способ предотвратить перекрытие с элементами пользовательского интерфейса системы полупрозрачные содержимое.


В следующем коде определяется темы с полупрозрачные состояния и панели навигации:

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

На снимке экрана ниже показано выше темы с состоянием полупрозрачные и панели навигации.

[![Снимок экрана примера приложения с полупрозрачные состояния и панели навигации](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Содержимое пользователя

### <a name="storage-access-framework"></a>Доступ к хранилищу Framework

Framework доступа хранилища (SAF) — это новый способ взаимодействия пользователей с хранимое содержимое, как изображения, видео и документы. Вместо представления пользователей с помощью диалогового окна, чтобы выбрать приложение для обработки содержимого, KitKat открывает новый пользовательский Интерфейс, который дает пользователям возможность доступа к данным в одном месте aggregate. После выбора содержимого пользователь вернется к приложения, запросившего содержимое, и работа приложений продолжится в обычном режиме.

Это изменение требует двух действий со стороны разработчика: во-первых, необходимо обновить к новому reqesting содержимого приложений, которым требуется содержимого от поставщиков. Во-вторых, приложения, которые записывают данные в `ContentProvider` должны быть изменены для использования новой платформы. Оба сценария зависят от нового [ `DocumentsProvider` ](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/) API.

#### <a name="documentsprovider"></a>DocumentsProvider

В KitKat взаимодействия с `ContentProviders` абстрагированы с `DocumentsProvider` класса. Это означает, что SAF безразлично, где данными является физически, пока доступен через `DocumentsProvider` API. Локального доступа к облачным службам и внешние устройства хранения данных используют интерфейс и обрабатываются одинаково, предоставление пользователю и разработчик взаимодействия с пользователем в одном месте.

В этом разделе описывается, как для загрузки и сохранения содержимого с помощью платформы доступа хранилища.

#### <a name="request-content-from-a-provider"></a>Содержимое запроса от поставщика

Мы расскажем KitKat, что нам нужно выбрать содержимое с помощью пользовательского интерфейса SAF с `ActionOpenDocument` назначение, которое означает, что нам нужно подключиться ко всем поставщикам содержимого доступной для устройства. Можно добавить некоторые фильтрацию, чтобы это назначение, указав `CategoryOpenable`, означающее только содержимое, которое может быть открыт (т. е. доступен, можно использовать содержимого) будет возвращено. KitKat также позволяет фильтровать содержимое с `MimeType`. Например, приведенный ниже код фильтров для результатов изображения, указав изображение `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

Вызов `StartActivityForResult` запускает SAF пользовательский Интерфейс, что пользователь сможет просматривать возможность выбирать изображение:

[![Снимок экрана примера приложения с помощью платформы доступа хранилища для переход к изображению](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

После пользователь выбрал образ, `OnActivityResult` возвращает `Android.Net.Uri` выбранного файла. В следующем примере отображается выбранный образ пользователя:

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

#### <a name="write-content-to-a-provider"></a>Запись содержимого к поставщику

Помимо загрузки содержимого из пользовательского интерфейса SAF, KitKat также позволяет сохранить содержимое в любой `ContentProvider` , реализующий `DocumentProvider` API. Сохранение содержимого использует `Intent` с `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

В приведенном выше примере загружает SAF пользовательского интерфейса, что дает пользователю возможность изменить имя файла и выберите каталог для размещения нового файла:

[![Снимок экрана, если пользователь изменит имя файла для NewDoc в каталоге загрузки](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Когда пользователь нажимает **Сохранить**, `OnActivityResult` передается `Android.Net.Uri` из только что созданный файл, который можно осуществить с помощью `data.Data`. Uri может использоваться для потоковой передачи данных в новый файл:

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

Обратите внимание, что [ `ContentResolver.OpenOutputStream(Android.Net.Uri)` ](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri)) возвращает `System.IO.Stream`, поэтому процесс потоковой передачи может быть написан на .NET.

Дополнительные сведения о загрузке, создания и редактирования содержимого с платформой доступа хранилища см. [Android документация Framework доступа хранилища](http://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Печать

Печать содержимого упрощено в KitKat с появлением [служб печати](https://developer.xamarin.com/api/namespace/Android.PrintServices/) и `PrintManager`. KitKat также является первой версии API в полной мере использовать [API Google печати облачной службы](https://developers.google.com/cloud-print/) с помощью [печати Google облачные приложения](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
Большинство устройств, которые поставляются вместе с KitKat автоматически загрузить приложение Google облака печати и [подключаемый модуль службы печати HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)при первом подключении к Wi-Fi. Пользователь может проверить параметры свой устройства печати, перейдя к **параметры > Система > Печать**:

[![Снимок экрана: пример Print screen параметров](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> Несмотря на то, что печати API-интерфейсы настроены для работы с Google облака печати по умолчанию, Android по-прежнему позволяет разработчикам Подготовка содержимого печати с помощью новых API и отправлять их в другие приложения могли обрабатывать печати.



#### <a name="printing-html-content"></a>Печать HTML-содержимого

Автоматически создает KitKat [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) для веб-представление с `WebView.CreatePrintDocumentAdapter`. Печать содержимого находится согласованного усилия между [ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) , ожидает HTML-содержимое для загрузки и позволяет действия знать, чтобы выбрать параметры печати, доступные в меню «Параметры» и Actvity, которая ожидает пользователю Выберите параметры печати и вызовы `Print`на `PrintManager`. В этом разделе рассматриваются основные настройку, необходимую для печати на экране HTML-содержимого.

Обратите внимание, что загрузки и печати веб-содержимого требуется разрешение Интернет:

[![Настройка разрешения Интернета в параметрах приложения](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Элемента меню печати

Команда «Печать» обычно будет отображаться в действии [меню параметров](http://developer.android.com/guide/topics/ui/menus.html#options-menu).
Меню «Параметры» позволяет пользователям выполнять действия в действии. Он находится в правом верхнем углу экрана и выглядит следующим образом:

[![Снимок экрана: пример печати Вывод элементов меню в правом верхнем углу экрана](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


Можно определить дополнительные пункты меню в *меню*каталог в разделе *ресурсов*. В следующем примере кода определяет образец пункт меню [печати](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

Взаимодействие с помощью меню "Параметры" в действии происходит через `OnCreateOptionsMenu` и `OnOptionsItemSelected` методы.
`OnCreateOptionsMenu` можно добавить новые пункты меню, таких как параметры печати из *меню* каталог ресурсов.
`OnOptionsItemSelected` ожидает передачи данных для пользователя, выбрав параметр печати из меню и начинает печати:

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

Приведенный выше код также определяет переменную с именем `dataLoaded` для отслеживания состояния HTML-содержимого. `WebViewClient` Будет значение этой переменной значение true, если были загружены все содержимое, чтобы сообщить действия для добавления элемента меню печати в меню «Параметры».

##### <a name="webviewclient"></a>WebViewClient

Задача `WebViewClient` необходимо, чтобы данные в `WebView` загружен полностью до печати параметр появляется в меню, это делается с `OnPageFinished` метод. `OnPageFinished` ожидает завершения загрузки веб-содержимого, а также указывает действие для повторного создания параметров меню с `InvalidateOptionsMenu`:

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

`OnPageFinished` также задает `dataLoaded` значение `true`, поэтому `OnCreateOptionsMenu` можно повторно создать меню с возможностью печати на месте.

##### <a name="printmanager"></a>PrintManager

В следующем примере кода на печать выводится содержимое `WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` принимает в качестве аргументов: имя для задания печати («MyWebPage» в этом примере), [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) , приводит к возникновению ошибки печать документа на основе содержимого, и [ `PrintAttributes` ](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/) (`null` в Пример выше). Можно указать `PrintAttributes` для размещения содержимого на странице, несмотря на то, что атрибуты по умолчанию должен обрабатывать большинство scenarions.

Вызов `Print` загружает печати пользовательского интерфейса, который содержит список параметров для задания печати. Пользовательский Интерфейс предоставляет пользователю возможность печати или сохранения содержимого HTML в PDF-файл, как показано на снимке экрана ниже:

[![Снимок экрана из PrintHtmlActivity отображение меню печати](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Снимок экрана из PrintHtmlActivity отображение сохранения в виде PDF-меню](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Оборудование

KitKat добавляет несколько интерфейсов API для поддержки новых возможностей устройства. Наиболее важные из них являются размещенное эмуляции карт и новых `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Эмуляции карты на основе узла в NFC

Размещенное эмуляции карты (HCE) позволяет приложениям ведут себя как NFC карт или чтения NFC карт не полагаться на элемент собственных Secure перевозчика. Перед настройкой HCE, убедитесь, HCE доступен на устройстве с `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

Требуется HCE функцию HCE и `Nfc` разрешение регистрироваться в приложении `AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Настройка разрешения NFC в параметрах приложения](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Для работы, HCE должна быть возможность работать в фоновом режиме и имеет для запуска, когда пользователь делает операция NFC, даже если приложение, с помощью HCE не выполняется. Это можно сделать, написав код HCE как `Service`. Реализует службу HCE `HostApduService` интерфейс, который реализует следующие методы:

-  *ProcessCommandApdu* -приложения протокола данных единицы (APDU) — что возвращает обмениваются чтения NFC и HCE службы. Этот метод использует ADPU из средства чтения и возвращает блок данных в ответ.

-  *OnDeactivated* - `HostAdpuService` отключается при HCE служба больше не взаимодействуют с помощью модуля чтения NFC.


Служба HCE также должна быть зарегистрирован в манифест приложения и назначив соответствующие разрешения, блокировка с намерением фильтра и метаданных. Следующий код является примером `HostApduService` зарегистрирован с помощью манифеста Android `Service` атрибут (Дополнительные сведения об атрибутах см. на странице Xamarin [работа с Android манифеста](~/android/platform/android-manifest.md) руководство):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("andorid.nfc.cardemulation.host.apdu_service",
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

Выше службы предоставляет способ чтения NFC для взаимодействия с приложением, но NFC чтения еще не может определить, если эта служба эмулирующий NFC карточки, необходимые для проверки. Чтобы средство чтения NFC идентификации службы, чтобы назначить службу уникальный *идентификатор приложения (AID)*. Мы укажите вспомогательное СРЕДСТВО, а также другие метаданные о службе HCE в файл ресурсов xml зарегистрирована `MetaData` атрибутов (см. приведенный выше код). Этот файл ресурсов указывает AID один или несколько фильтров — уникальный идентификатор строки в шестнадцатеричном формате, которые соответствуют возможностям один или несколько устройств чтения NFC:

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

Помимо фильтры AID файла ресурсов xml также содержит описание пользовательского интерфейса службы HCE указывает группу AID (приложение оплаты и «другие») и в случае с приложением оплаты, 260 x 96 баннера точки распространения для отображения для пользователя.

Программа установки, описанные выше предоставляет основные стандартные блоки для приложения, имитируя карточки NFC. NFC сам требуется несколько дополнительных действий и дальнейшего тестирования для настройки. Дополнительные сведения о размещенное эмуляции карт см. [портал документации Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Дополнительные сведения об использовании NFC с Xamarin извлечь [Xamarin NFC образцы](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Датчики

KitKat предоставляет доступ к датчики устройства через [ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/).
`SensorManager` , Позволяет операционной системе расписания доставки данных датчика к приложению в виде пакетов, сохраняя аккумулятора.

KitKat поставляется с два новых типа датчика для отслеживания действий пользователя. Они основаны на акселерометр и включают:

-  *StepDetector* -App уведомление или пробуждении при выполнении пользователем соответствующего шага и детектор предоставляет значение времени, когда шага произошла.

-  *StepCounter* -отслеживает число действий пользователь так, как было зарегистрировано датчика *только после перезагрузки устройства*.

На следующем снимке экрана показаны счетчику шаг в действии:

[![Снимок экрана приложения SensorsActivity отображение счетчика шаг](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Можно создать `SensorManager` путем вызова `GetSystemService(SensorService)` и приведение результат в виде `SensorManager`. Чтобы использовать шаг счетчика, вызвать `GetDeafultSensor` на `SensorManager`. Можно регистрировать датчика и прослушивает изменения число шаге с помощью [ `ISensorEventListener` ](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/) интерфейс, как показано в следующем примере:

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

`OnSensorChanged` вызывается, если число шаг обновления, пока приложение находится на переднем плане. Если приложение входит в фоновом режиме, или устройство находится в спящем режиме, `OnSensorChanged` не будет вызван; тем не менее, шаги будут продолжать до `UnregisterListener` вызывается.

Имейте в виду, что *значение шага накопительного пакета для всех приложений, которые регистрируют датчика*. Это означает, что даже в том случае, если необходимо удалить и переустановить приложение и инициализировать `count` переменной 0 при запуске приложения, значений, передаваемых датчиком останется общее число шагов, выполняемых во время регистрации датчика, как по вашей приложение или другой. Вы можете предотвратить приложения от добавления счетчика шаг путем вызова `UnregisterListener` на `SensorManager`, как показано в следующем примере кода:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Перезагрузка устройства Сбрасывает счетчик шаг в 0. Приложения потребуется дополнительный код, чтобы убедиться, что он сообщает, точные сведения для приложения, независимо от других приложений с помощью датчика или состояние устройства.


> [!NOTE]
> При API для шага обнаружения и инвентаризации поставляется с KitKat не все телефоны будут развернуты с датчиком. Можно проверить, если датчик доступна, выполнив `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`, или убедитесь, возвращаемым значением `GetDefaultSensor` не `null`.


<a name="developer_tools" />

## <a name="developer-tools"></a>Средства разработчика

### <a name="screen-recording"></a>Запись экрана

KitKat включает новый экран, возможности записи, чтобы разработчики могут записывать приложения в действии. Запись экрана доступна через [Android мост отладки (ADB)](http://developer.android.com/tools/help/adb.html) клиента, который можно загрузить в составе пакета SDK для Android.

Чтобы запись экрана, подключите устройство; затем найдите установки Android SDK, перейдите в каталог **средства платформы** каталога и выполнения **adb** клиента:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

Приведенная выше команда будет записывать видео по умолчанию 3 минуты в разрешении 4Mbps по умолчанию. Чтобы изменить длину, добавьте *--предел времени* флаг.
Чтобы изменить разрешение, добавить *--скорость* флаг. Следующая команда будет записывать видео минуту долго 8Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Видео можно найти на вашем устройстве — он будет отображаться в галерее по завершении записи.


## <a name="other-kitkat-additions"></a>Другие дополнения KitKat

Помимо изменения, описанные выше KitKat позволяет:

-  *Используйте в полноэкранном режиме* -KitKat появилось новое [Иммерсивном режиме](http://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) для просмотра содержимого, игры и выполнения других приложений, которые выиграют от качества полноэкранном режиме.

-  *Изменить вид уведомлений,* -получить дополнительные сведения о системных уведомлений с [ `NotificationListenerService` ](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) . Это позволяет представить данные по-разному внутри приложения.

-  *Зеркальное отображение Drawable ресурсов* -Drawable ресурсы имеют новый [ `autoMirrored` ](http://developer.android.com/reference/android/R.attr.html#autoMirrored) атрибут, который сообщает системе, создания зеркальной версии для изображений, требующих перевода для макетов справа налево.

-  *Приостанавливать анимации* -Приостановка и возобновление анимации, созданные с [ `Animator` ](https://developer.xamarin.com/api/type/Android.Animation.Animator/) класса.

-  *Чтение динамически изменение текста* -обозначают части пользовательского интерфейса, динамическое обновление новым текстом как «динамической области» с новым [ `accesibilityLiveRegion` ](http://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion) атрибута, новый текст будет считываться автоматически в режиме специальных возможностей.

-  *Улучшение аудио* -Создание громче отслеживает с [ `LoudnessEnhancer` ](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/) , найти пиковые и RMS аудиопотока с [ `Visualizer` ](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/) класса, а также получать сведения из [аудио timestamp](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/) для синхронизации звука видео.

-  *Синхронизация ContentResolver через настраиваемый промежуток времени* -KitKat некоторых особенностей добавляет ко времени, которое выполняется запрос на синхронизацию. Синхронизация `ContentResolver` в пользовательских время или интервал, вызвав `ContentResolver.RequestSync` и передача в `SyncRequest`.

-  *Различать между контроллерами* -KitKat в контроллерах назначаются идентификаторы уникальным целым числом, которые можно получить с помощью устройства `ControllerNumber` свойство. Это упрощает сообщить друг от друга игроков в игру.

-  *Удаленное управление* -несколько изменений оборудования и программного обеспечения стороны KitKat позволяет включать развернуты радиочастотной IR удаленного управления с помощью устройства `ConsumerIrService`, периферийные устройства с новой ивзаимодействоватьс[ `RemoteController` ](https://developer.xamarin.com/api/type/Android.Media.RemoteController/) API-интерфейсы.

Дополнительные сведения об изменениях API выше обратитесь Google [Android 4.4 API-интерфейсы](http://developer.android.com/about/versions/android-4.4.html) Обзор.


## <a name="summary"></a>Сводка

В этой статье представлены некоторые новые интерфейсы API, доступные в Android 4.4 (API уровня 19) и рекомендации, описанные при переходе приложения в KitKat. Возникают указанные изменения в API-интерфейсы влияния на пользователя, включая ИТ *перехода framework* и новые параметры для *темы*. Затем он появился *Framework доступа к хранилищу* и `DocumentsProvider` класса, а также новый *печать API-интерфейсы*. Она изучена *NFC карты на основе узла эмуляции* и работа с *маломощных датчики*, включая два новых датчика для отслеживания действий пользователя. Наконец, демонстрируется захват в режиме реального времени демонстрации приложений с *запись экрана*, предоставленные подробный список KitKat API изменения и дополнения.


## <a name="related-links"></a>Связанные ссылки

- [Образец KitKat](https://developer.xamarin.com/samples/KitKat/)
- [Android 4.4 API-интерфейсы](http://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](http://developer.android.com/about/versions/kitkat.html)
