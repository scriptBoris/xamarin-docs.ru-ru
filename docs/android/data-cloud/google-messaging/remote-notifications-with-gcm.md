---
title: Удаленный уведомления с Google Cloud Messaging
description: Это пошаговое руководство содержит пошаговое объяснение того, как реализуется с помощью Google Cloud Messaging удаленного уведомлений (также называемые push-уведомлений) в приложении Xamarin.Android. Здесь описываются различные классы, которые необходимо реализовать для обмена данными с Google Cloud Messaging (GCM), здесь объясняется, как настраивать разрешения в Android манифеста для доступа к GCM, а также демонстрирует конца в конец обмен сообщениями с образцом программы тестирования.
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: f4a1451cb848f4da1f595c15d946f4e05292900d
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31045137"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Удаленный уведомления с Google Cloud Messaging

_Это пошаговое руководство содержит пошаговое объяснение того, как реализуется с помощью Google Cloud Messaging удаленного уведомлений (также называемые push-уведомлений) в приложении Xamarin.Android. Здесь описываются различные классы, которые необходимо реализовать для обмена данными с Google Cloud Messaging (GCM), здесь объясняется, как настраивать разрешения в Android манифеста для доступа к GCM, а также демонстрирует конца в конец обмен сообщениями с образцом программы тестирования._

> [!NOTE]
> GCM был заменен с [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM сервер и клиентские API [являются устаревшими](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) и больше не будут доступны как можно скорее 11 апреля 2019 г.

## <a name="gcm-notifications-overview"></a>Общие сведения о уведомления GCM

В этом пошаговом руководстве мы создадим Xamarin.Android приложение, которое использует Google Cloud Messaging (GCM) для реализации удаленного уведомлений (также известный как *push-уведомления*). Реализуется на различные службы назначение и прослушиватель, использующие GCM для удаленного обмена сообщениями, и мы протестируем реализацию с программой командной строки, которая имитирует сервера приложений. 

Обратите внимание, что обмен сообщениями Firebase облака (FCM) новой версией GCM &ndash; Google настоятельно рекомендует использование FCM вместо GCM. Если вы используете GCM, рекомендуется обновление до FCM. Дополнительные сведения о FCM см. в разделе [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

Прежде чем перейти в этом пошаговом руководстве, необходимо получить необходимые учетные данные, используемые серверы GCM Google; Этот процесс описан в [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md). В частности, вам потребуется *ключ API* и *идентификатор отправителя* для вставки в пример кода, в данном пошаговом руководстве. 

Чтобы создать клиентское приложение включен GCM Xamarin.Android, мы будем использовать следующие шаги:

1.  Установите дополнительные пакеты, необходимые для взаимодействия с серверами GCM.
2.  Настройка разрешений для приложений для доступа к серверам GCM.
3.  Реализуйте код для проверки наличия службы Google Play. 
4.  Реализуйте намерения службы регистрации, которая согласовывает с GCM для токена регистрации.
5.  Реализации службы прослушивателя идентификатор экземпляра, ожидающий обновления токена регистрации из GCM.
6.  Реализация службы GCM прослушивателя, получающую удаленного с сервера приложений через GCM.

Это приложение будет использовать новую функцию GCM, известный как *обмена сообщениями в разделе*. В разделе обмена сообщениями, сервер приложений отправляет сообщение на раздел, а не список отдельных устройств. Устройства, которые подписаны на этот раздел можно получать сообщения раздела как push-уведомлений. Дополнительные сведения об обмене сообщениями разделе GCM см. в разделе Google [реализации системы обмена сообщениями разделе](https://developers.google.com/cloud-messaging/topic-messaging). 

Когда клиентское приложение будет готово, мы будем реализовать командной строки C# приложение, которое отправляет push-уведомлений для наших клиентского приложения через GCM. 

## <a name="walkthrough"></a>Пошаговое руководство

Чтобы начать, давайте создадим новое пустое решение с именем **RemoteNotifications**. Далее добавим в это решение на основе проекта Android **приложения Android** шаблона. Давайте назовем этот проект **ClientApp**. (Если вы не знакомы с созданием проектам Xamarin.Android, см. раздел [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md).) **ClientApp** проект будет содержать код для клиентского приложения Xamarin.Android, получает удаленный уведомления через GCM. 

### <a name="add-required-packages"></a>Добавить необходимые пакеты

Прежде чем мы можно реализовать наш код клиента приложения, мы необходимо установить несколько пакетов, которые мы будем использовать для связи с GCM. Кроме того необходимо добавить приложение магазина Google Play устройство, если он еще не установлен.

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>Добавьте пакет GCM служб Xamarin Google Play

Для получения сообщений от Google Cloud Messaging [службы Google Play](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/) framework должна быть установлена на устройство. Без этой платформы приложение не может получать сообщения от серверы GCM. Службы Google Play выполняется в фоновом режиме при включении устройства Android, без вмешательства пользователя прослушивание сообщений из GCM. При поступлении этих сообщений, службы Google Play преобразовывает сообщения в целей и затем передает эти цели для приложений, которые зарегистрированы для них. 

В Visual Studio щелкните правой кнопкой мыши **References > Управление пакетами NuGet...** ; в Visual Studio для Mac, щелкните правой кнопкой мыши **пакеты > Добавление пакетов...** . Поиск **Xamarin службы Google Play - GCM** и установить этот пакет в **ClientApp** проекта: 

[![Установка служб Google Play](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

При установке **Xamarin службы Google Play - GCM**, **Xamarin службы Google Play - Base** устанавливается автоматически. Если возникает ошибка, преобразовать в проект *Android минимум целевой* задавать значение, отличное от **компиляция с помощью пакета SDK версии** и повторите попытку установки NuGet. 

Далее следует изменить **MainActivity.cs** и добавьте следующее `using` инструкции:

```csharp
using Android.Gms.Common;
using Android.Util;
```

Это делает типы в пакете GMS служб воспроизвести Google для нашего кода, а также добавляет функции ведения журнала, который будет использоваться для отслеживания операций с GMS. 

#### <a name="google-play-store"></a>Магазин Google Play

Для получения сообщений из GCM, приложение магазина Google Play должны устанавливаться на устройстве. (При установке приложения Google Play на устройстве, магазина Google Play также устанавливается, поэтому вполне вероятно, уже установлен на вашем устройстве тестирования.) Без Google Play приложение не может получать сообщения от GCM. Если вы еще не установленное на вашем устройстве приложение магазина Google Play, посетите [Google Play](https://support.google.com/googleplay) веб-сайта для загрузки и установки Google Play. 

Кроме того можно использовать эмулятор Android под управлением Android 2.2 или более поздней версии, вместо тестовое устройство (не требуется для установки магазина Google Play на эмулятор Android). Если вы используете эмулятор, необходимо использовать для подключения к GCM Wi-Fi и необходимо открыть несколько портов в брандмауэре Wi-Fi, как описано далее в этом пошаговом руководстве. 

### <a name="set-the-package-name"></a>Задайте имя пакета

В [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md), мы указали имя пакета для нашего приложения с поддержкой GCM (это имя пакета также служит в качестве *идентификатор приложения* связанное с нашими ключ API и идентификатор отправителя). Откроем свойства **ClientApp** проект и задайте имя пакета для данной строки. В этом примере задается имя пакета `com.xamarin.gcmexample`:

[![Задание имени пакета](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

Обратите внимание, что клиентское приложение не сможет получать маркер регистрации из GCM, если это имя пакета не *точно* совпадает с именем пакета, мы ввели в консоли разработчика Google. 

### <a name="add-permissions-to-the-android-manifest"></a>Добавить разрешения для манифеста Android

Приложения должны иметь следующие разрешения настроена, прежде чем он может получать уведомления от Google Cloud Messaging: 

-   `com.google.android.c2dm.permission.RECEIVE` &ndash; Позволяет приложению регистрироваться и получать сообщения от Google Cloud Messaging. (Что делает `c2dm` означают? Это означает _облако для обмена сообщениями на устройстве_, который является предшественником неподдерживаемые для GCM. 
    По-прежнему использует GCM `c2dm` во многих строк его разрешений.) 

-   `android.permission.WAKE_LOCK` &ndash; (Необязательно) Запрещает устройству ЦП из переход в спящий режим во время ожидания для сообщения. 

-   `android.permission.INTERNET` &ndash; Предоставляет доступ к Интернету, чтобы клиентское приложение может взаимодействовать с GCM. 

-   *имя_пакета* `.permission.C2D_MESSAGE` &ndash; регистрирует приложение в Android и запрашивает разрешение на получение всех C2D исключительно сообщения (облака на устройство). *Имя_пакета* префикс является таким же, как идентификатор вашего приложения. 

Мы настроим эти разрешения в манифесте Android. Изменим **AndroidManifest.xml** и заменить его содержимое следующим кодом XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
    package="YOUR_PACKAGE_NAME" 
    android:versionCode="1" 
    android:versionName="1.0" 
    android:installLocation="auto">
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" />
    <permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" 
                android:protectionLevel="signature" />
    <application android:label="ClientApp" android:icon="@drawable/Icon">
    </application>
</manifest>
```

В приведенном выше коде XML, изменить *YOUR_PACKAGE_NAME* имя пакета для проекта клиентского приложения. Например, `com.xamarin.gcmexample`. 

### <a name="check-for-google-play-services"></a>Проверка службы Google Play

В данном пошаговом руководстве мы создаем приложения элементарного с одним `TextView` в пользовательском Интерфейсе. Это приложение не указывает непосредственно взаимодействие с GCM. Вместо этого мы посмотрим, в окне вывода, чтобы увидеть как нашей подтверждений приложения с GCM, и мы свяжемся область уведомлений для новых уведомлений по мере их поступления. 

Во-первых давайте создадим макет в области сообщений. Изменить **Resources.layout.Main.axml** и заменить его содержимое следующим кодом XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

Сохранить **Main.axml** и закройте его.

При запуске клиентского приложения, необходимо для проверки доступности службы Google Play перед предпринимается попытка связаться GCM. Изменить **MainActivity.cs** и замените ``count`` экземпляра объявление переменной со следующим объявлением переменной экземпляр: 

```csharp
TextView msgText;
```

Добавьте следующий метод **MainActivity** класса: 

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "Sorry, this device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

Этот код проверяет устройстве, чтобы определить, установлен ли APK воспроизведение служб Google. Если он не установлен, появится в тексте сообщения, указывает, что пользователю загрузить APK из магазина Google Play (или включить ее в параметрах системы на устройстве). Поскольку мы хотим выполнить эту проверку при запуске клиентского приложения, мы добавим вызов этого метода в конце `OnCreate`. 


Затем замените `OnCreate` метод следующим кодом:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

Этот код проверяет наличие APK воспроизведение служб Google и записывает результаты в области сообщений. 

Давайте полностью повторное построение и запуск приложения. Появится экран, который выглядит как следующий снимок экрана: 

[![Доступные службы Google Play](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

Если этот результат не получен, APK воспроизведение служб Google проверить, установлен ли на устройстве и, **Xamarin службы Google Play - GCM** пакет будет добавлен к вашей **ClientApp** проекта см. раздел ранее. Если возникли ошибки сборки, попробуйте очистки решения и сборка проекта еще раз. 

Далее мы записываем код подключиться к GCM и вернуть маркер регистрации.

### <a name="register-with-gcm"></a>Зарегистрировать GCM

Прежде чем приложение может получать уведомления удаленного с сервера приложений, необходимо зарегистрировать GCM и вернуть маркер регистрации. Обрабатывается рабочих зарегистрировать приложение в GCM `IntentService` , мы создадим. Наш `IntentService` выполняет следующие действия: 

1.  Использует [InstanceID](https://developers.google.com/instance-id/) API для формирования токенов безопасности, который разрешает нашего приложения клиента для доступа к серверу приложений. Взамен мы получаем регистрации токена из GCM.

2.  Пересылает маркер регистрации на сервер приложений (если сервер приложений требуется его).

3.  Подписывается на один или несколько каналов уведомления раздела.

После мы реализовали это `IntentService`, мы протестируем его для просмотра, если мы получаем регистрации токена из GCM.

Добавьте новый файл с именем **RegistrationIntentService.cs** и замените код шаблона следующим:


```csharp
using System;
using Android.App;
using Android.Content;
using Android.Util;
using Android.Gms.Gcm;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false)]
    class RegistrationIntentService : IntentService
    {
        static object locker = new object();

        public RegistrationIntentService() : base("RegistrationIntentService") { }

        protected override void OnHandleIntent (Intent intent)
        {
            try
            {
                Log.Info ("RegistrationIntentService", "Calling InstanceID.GetToken");
                lock (locker)
                {
                    var instanceID = InstanceID.GetInstance (this);
                    var token = instanceID.GetToken (
                        "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);

                    Log.Info ("RegistrationIntentService", "GCM Registration Token: " + token);
                    SendRegistrationToAppServer (token);
                    Subscribe (token);
                }
            }
            catch (Exception e)
            {
                Log.Debug("RegistrationIntentService", "Failed to get a registration token");
                return;
            }
        }

        void SendRegistrationToAppServer (string token)
        {
            // Add custom implementation here as needed.
        }

        void Subscribe (string token)
        {
            var pubSub = GcmPubSub.GetInstance(this);
            pubSub.Subscribe(token, "/topics/global", null);
        }
    }
}
```

В приведенный выше образец кода, измените *YOUR_SENDER_ID* с номером идентификатора отправителя для проекта клиентского приложения. Для получения идентификатора отправителя для проекта: 

1.  Войдите на [облачной консоли Google](https://console.cloud.google.com/) и выберите имя проекта в раскрывающемся меню. В **проекта сведения** область, которая отображается для проекта, нажмите кнопку **перейти к параметрам проекта**:

    [![При выборе проекта XamarinGCM](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2.  На **параметры** найдите **номер проекта** &ndash; — идентификатор отправителя для проекта:

    [![Отображается номер проекта](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

Мы хотим начать нашей `RegistrationIntentService` при запуске нашего приложения. Изменить **MainActivity.cs** и изменения `OnCreate` метод, чтобы наши `RegistrationIntentService` запускается после того, проверяется на наличие службы Google Play: 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView(Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    if (IsPlayServicesAvailable ())
    {
        var intent = new Intent (this, typeof (RegistrationIntentService));
        StartService (intent);
    }
}
```

Теперь давайте рассмотрим каждый раздел `RegistrationIntentService` для понимания принципов его работы. 

Во-первых, мы аннотировать нашей `RegistrationIntentService` с помощью следующего атрибута означает нашей службе не для создания экземпляра системой: 

```csharp
[Service (Exported = false)]
```

`RegistrationIntentService` Конструктор имена рабочий поток *RegistrationIntentService* чтобы упростить отладку. 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

Основные функциональные возможности `RegistrationIntentService` находится в `OnHandleIntent` метод. Рассмотрим этот код, чтобы увидеть, как он регистрирует нашего приложения с GCM.


##### <a name="request-a-registration-token"></a>Запрос токена регистрации

`OnHandleIntent` сначала вызывает Google [InstanceID.GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;) метод для запроса токена регистрации из GCM. Мы поместить этот код в `lock` Чтобы защититься от возможности несколько способов регистрации выполняются одновременно &ndash; `lock` гарантирует, что этих целей обрабатываются последовательно. Если мы не удалось получить маркер регистрации, возникает исключение и мы записать ошибку в журнал. Если регистрация прошла успешно, `token` задан маркер регистрации, мы получали из GCM: 

```csharp
static object locker = new object ();
...
try
{
    lock (locker)
    {
        var instanceID = InstanceID.GetInstance (this);
        var token = instanceID.GetToken (
            "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);
        ...
    }
}
catch (Exception e)
{
    Log.Debug ...
```

##### <a name="forward-the-registration-token-to-the-app-server"></a>Пересылать маркер регистрации на сервер приложений

Если мы получаем маркер регистрации (то есть не было создано исключение) вызывается метод `SendRegistrationToAppServer` для регистрации пользователя связи токена с учетной записью стороне сервера (если таковые имеются), обслуживаемый нашего приложения. Так как эта реализация зависит от структуры приложения сервера, пустой метод представлена ниже: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

В некоторых случаях сервер приложений не требуется маркер регистрации пользователя; в этом случае этот метод может быть опущено. При отправке на сервер приложений, маркер регистрации `SendRegistrationToAppServer` должен поддерживать значение типа boolean для указания, было ли отправлено токен на сервер. Если это логическое выражение имеет значение false, `SendRegistrationToAppServer` отправляет этот токен на сервер приложений &ndash; в противном случае маркер уже был отправлен на сервер приложений в предыдущем вызове. 


##### <a name="subscribe-to-the-notification-topic"></a>Подпишитесь на раздела уведомлений

Далее мы называем нашей `Subscribe` метод для указания для GCM, который необходимо подписаться на раздела уведомлений. В `Subscribe`, мы называем [GcmPubSub.Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) API подписаться нашего приложения клиента для всех сообщений в группе `/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

Сервер приложений должен отправлять сообщения уведомления `/topics/global` , чтобы получать их. Обратите внимание, что имя раздела в группе `/topics` может быть любым требуется, при условии, что сервер приложений и клиентского приложения согласовать эти имена. (Здесь мы выбрали имя `global` для указания, что мы хотим получать сообщения во всех статьях, поддерживаемые сервером приложения.) 

Сведения о разделе GCM обмена сообщениями на стороне сервера см. в разделе Google [отправки сообщений на разделы](https://developers.google.com/cloud-messaging/topic-messaging). 


#### <a name="implement-an-instance-id-listener-service"></a>Реализация прослушивателя идентификатор экземпляра службы

Маркеры регистрации являются уникальными и защищен; Однако клиентское приложение (или GCM) может потребоваться обновить маркер регистрации в случае повторной установки приложения или проблему безопасности. По этой причине мы реализовали `InstanceIdListenerService` , отвечает на запросы токена обновления от GCM. 

Добавьте новый файл с именем **InstanceIdListenerService.cs** и замените код шаблона следующим: 

```csharp
using Android.App;
using Android.Content;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
    class MyInstanceIDListenerService : InstanceIDListenerService
    {
        public override void OnTokenRefresh()
        {
            var intent = new Intent (this, typeof (RegistrationIntentService));
            StartService (intent);
        }
    }
}
```

Добавление заметок `InstanceIdListenerService` с помощью следующего атрибута, чтобы указать, что служба находится не для создания экземпляра системой и что он может получить маркер регистрации GCM (также называемый *идентификатор экземпляра*) обновляют запросы: 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

`OnTokenRefresh` Метод в наша служба запускает `RegistrationIntentService` , чтобы его можно перехватить новый маркер регистрации.


#### <a name="test-registration-with-gcm"></a>Тестирование регистрации GCM

Давайте полностью повторное построение и запуск приложения. Если вы успешно получили маркер регистрации из GCM, маркер регистрации должны отображаться в окне вывода. Пример: 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>Дескриптор подчиненный сообщений 

Код, который мы реализовали сих — только код «и»; проверяется, установлен и согласовывает с GCM и сервер приложений для подготовки нашей клиентское приложение для получения уведомлений об удаленной службы Google Play. Однако у нас есть еще реализовать код, который фактически получать и обрабатывать сообщения подчиненных уведомления. Чтобы сделать это, необходимо реализовать *служба прослушивания GCM*. Эта служба получает сообщения раздела от сервера приложений и локально осуществляет широковещательную рассылку их как уведомления. После реализуем эту службу, мы создадим тестовую программу для отправки сообщений в GCM, чтобы мы могли видеть, если реализацию работает правильно. 


#### <a name="add-a-notification-icon"></a>Добавить значок уведомления

Давайте сначала добавить мелкого значка, который будет отображаться в области уведомлений при запуске наши уведомления. Можно скопировать [этот значок](remote-notifications-with-gcm-images/ic-stat-ic-notification.png) в проект или создать собственный пользовательский значок. Мы назовем файл значка **ic_stat_button_click.png** и скопируйте его в **ресурсы или drawable** папки. Не забывайте использовать **Добавить > существующий элемент...**  для включения этого значка файла в проект.


#### <a name="implement-a-gcm-listener-service"></a>Реализация прослушивателя службы GCM

Добавьте новый файл с именем **GcmListenerService.cs** и замените код шаблона следующим:

```csharp
using Android.App;
using Android.Content;
using Android.OS;
using Android.Gms.Gcm;
using Android.Util;

namespace ClientApp
{
    [Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
    public class MyGcmListenerService : GcmListenerService
    {
        public override void OnMessageReceived (string from, Bundle data)
        {
            var message = data.GetString ("message");
            Log.Debug ("MyGcmListenerService", "From:    " + from);
            Log.Debug ("MyGcmListenerService", "Message: " + message);
            SendNotification (message);
        }

        void SendNotification (string message)
        {
            var intent = new Intent (this, typeof(MainActivity));
            intent.AddFlags (ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity (this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                .SetSmallIcon (Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle ("GCM Message")
                .SetContentText (message)
                .SetAutoCancel (true)
                .SetContentIntent (pendingIntent);

            var notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
            notificationManager.Notify (0, notificationBuilder.Build());
        }
    }
}
```

Давайте рассмотрим каждый раздел нашей `GcmListenerService` для понимания принципов его работы. 

Во-первых, мы заметки `GcmListenerService` с атрибут, указывающий, что эта служба является не создаваться системой, и мы приводим блокировка с намерением фильтра, чтобы указать, что он получает сообщения, GCM: 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

Когда `GcmListenerService` получает сообщение от GCM, `OnMessageReceived` вызывается метод. Этот метод извлекает содержимое сообщения из переданного `Bundle`, записывает содержимое сообщения (чтобы мы могли просмотреть в окне «Вывод») и вызывает метод `SendNotification` для запуска локального уведомления с помощью полученного сообщения содержимого: 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

`SendNotification` Использует метод `Notification.Builder` для создания уведомления, а затем использует `NotificationManager` для запуска уведомления. По сути это преобразует удаленного уведомление в локальном уведомления отображаются для пользователя.
Дополнительные сведения об использовании `Notification.Builder` и `NotificationManager`, в разделе [локального уведомления](~/android/app-fundamentals/notifications/local-notifications.md).


#### <a name="declare-the-receiver-in-the-manifest"></a>Объявите получателя в манифесте

Прежде чем мы может получать сообщения из GCM, мы должны объявлять прослушивателя GCM в Android манифест. Изменим **AndroidManifest.xml** и замените `<application>` раздел следующий XML-код: 

```xml
<application android:label="RemoteNotifications" android:icon="@drawable/Icon">
    <receiver android:name="com.google.android.gms.gcm.GcmReceiver" 
              android:exported="true" 
              android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="YOUR_PACKAGE_NAME" />
        </intent-filter>
    </receiver>
</application>
```

В приведенном выше коде XML, изменить *YOUR_PACKAGE_NAME* имя пакета для проекта клиентского приложения. В нашем примере Пошаговое руководство, имя пакета — `com.xamarin.gcmexample`. 

Давайте рассмотрим, что делает каждый параметр в этот XML-код:

|Параметр|Описание|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|Объявляет, что нашего приложения реализует приемник GCM, который перехватывает и обрабатывает входящие сообщения push-уведомлений.|
|`com.google.android.c2dm.permission.SEND`|Объявляет, что только серверы GCM может посылать сообщения этому приложению.|
|`com.google.android.c2dm.intent.RECEIVE`|Блокировка с намерением фильтр рекламу обработки широковещательных сообщений из GCM нашего приложения.|
|`com.google.android.c2dm.intent.REGISTRATION`|Блокировка с намерением фильтра рекламу обработки новые способы регистрации в нашем приложении (то есть, были реализованы служба прослушивания идентификатор экземпляра).|

Кроме того, вы можете декорировать `GcmListenerService` с этими атрибутами, а не их указания в формате XML; здесь мы указывать их в **AndroidManifest.xml** таким образом, проще выполнить примеры кода. 


### <a name="create-a-message-sender-to-test-the-app"></a>Создание отправителя сообщения тестирование приложения

Давайте добавьте в решение проект рабочего стола консольного приложения C# и назовите его **MessageSender**. Мы будем использовать это консольное приложение для имитации сервер приложений &ndash; будет отправлять сообщения уведомления **ClientApp** через GCM. 


#### <a name="add-the-jsonnet-package"></a>Добавьте пакет Json.NET

Это консольное приложение мы создаем полезные данные JSON, содержащий сообщение уведомления, которые необходимо отправить в клиентское приложение. Мы будем использовать **Json.NET** пакета в **MessageSender** для упрощения построения объекта JSON, предусмотренного GCM. В Visual Studio щелкните правой кнопкой мыши **References > Управление пакетами NuGet...** ; в Visual Studio для Mac, щелкните правой кнопкой мыши **пакеты > Добавление пакетов...** . 

Давайте поиск **Json.NET** пакет и установите его в проект: 

[![Установка Json.NET пакета](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)


#### <a name="add-a-reference-to-systemnethttp"></a>Добавьте ссылку на System.Net.Http

Нам также нужно добавить ссылку на `System.Net.Http` , можно создать экземпляр `HttpClient` для отправки в GCM нашей тестовое сообщение. В **MessageSender** проекта, щелкните правой кнопкой мыши **References > Добавить ссылку** и прокрутите вниз до **System.Net.Http**. Установите флажок рядом с **System.Net.Http** и нажмите кнопку **ОК**. 


#### <a name="implement-code-that-sends-a-test-message"></a>Реализовать код, который отправляет тестовое сообщение

В **MessageSender**, изменить **Program.cs** и заменить его содержимое следующим кодом:

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;

namespace MessageSender
{
    class MessageSender
    {
        public const string API_KEY = "YOUR_API_KEY";
        public const string MESSAGE = "Hello, Xamarin!";

        static void Main (string[] args)
        {
            var jGcmData = new JObject();
            var jData = new JObject();

            jData.Add ("message", MESSAGE);
            jGcmData.Add ("to", "/topics/global");
            jGcmData.Add ("data", jData);

            var url = new Uri ("https://gcm-http.googleapis.com/gcm/send");
            try
            {
                using (var client = new HttpClient())
                {
                    client.DefaultRequestHeaders.Accept.Add(
                        new MediaTypeWithQualityHeaderValue("application/json"));

                    client.DefaultRequestHeaders.TryAddWithoutValidation (
                        "Authorization", "key=" + API_KEY);

                    Task.WaitAll(client.PostAsync (url,
                        new StringContent(jGcmData.ToString(), Encoding.Default, "application/json"))
                            .ContinueWith(response =>
                            {
                                Console.WriteLine(response);
                                Console.WriteLine("Message sent: check the client device notification tray.");
                            }));
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Unable to send GCM message:");
                Console.Error.WriteLine(e.StackTrace);
            }
        }
    }
}
```

В приведенном выше коде измените *YOUR_API_KEY* ключ API для проекта клиентского приложения. 

Приложение этот тестовый сервер отправляет следующее сообщение, отформатированное по JSON GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM, в свою очередь, передает это сообщение, чтобы клиентское приложение. Создадим **MessageSender** и откройте окно консоли, где его можно было запустить из командной строки.



### <a name="try-it"></a>Попробуйте!

Теперь все готово для тестирования нашего приложения клиента. Если вы используете эмулятор, или если устройство взаимодействует с GCM по Wi-Fi, необходимо открыть следующие порты TCP в брандмауэре сообщения GCM получить с помощью: 5228, 5229 и 5230.

Запустите клиентское приложение и просмотреть в окне вывода. После `RegistrationIntentService` успешно получении регистрацию токена из GCM, в окне вывода отображать маркер с выходные данные журнала, аналогичный следующему:

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

На этом этапе в клиентском приложении готов к получению удаленного уведомление. Запустите из командной строки, **MessageSender.exe** программе отправлять сообщение «Hello, Xamarin» уведомления в клиентское приложение.
Если еще не построен **MessageSender** проекта, сделайте это сейчас.

Для запуска **MessageSender.exe** в Visual Studio, откройте командную строку, измените **MessageSender/bin/Debug** каталог и выполните команду напрямую:

```cmd
MessageSender.exe
```

Для запуска **MessageSender.exe** в Visual Studio для Mac, откройте сеанс терминала, измените **MessageSender/bin/Debug** каталога и моно использовать для запуска **MessageSender.exe** 

```bash
mono MessageSender.exe
```

Он может занять до минуты для сообщения для распространения через GCM и до клиентского приложения. При получении сообщения мы должны выводиться данные, аналогичный следующему в окне вывода: 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

Кроме того вы заметите, что новый значок уведомления отображается в области уведомлений: 

[![На устройстве появляется значок Notiication](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

При открытии панели уведомлений для просмотра уведомлений о вы увидите, что наши удаленного уведомления:

[![Сообщение уведомления](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

Итак, приложение получил его первого удаленного уведомления.

Обратите внимание, что GCM сообщения больше не будут приниматься, если приложение остановлено force. Чтобы возобновить уведомления после остановки force, приложение должно быть перезапустить вручную. Дополнительные сведения об этой политике Android см. в разделе [запуск элементов управления на остановленные приложения](https://developer.android.com/about/versions/android-3.1.html#launchcontrols) и это [post переполнения стека](http://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267). 

 
## <a name="summary"></a>Сводка

В этом пошаговом руководстве подробные шаги по реализации удаленного уведомления в приложении Xamarin.Android. Он описывается установка дополнительных пакетов, необходимые для обмена данными GCM и его объясняется настройка разрешений для приложений для доступа к серверам GCM. Он предоставляется пример кода, иллюстрирующий проверка на наличие службы Google Play, как реализовать блокировка с намерением обновления регистрации и служба прослушивания идентификатор экземпляра, которая согласовывает с GCM маркер регистрации и способы реализации прослушивателя GCM Служба, которая получает и обрабатывает сообщения удаленной уведомления. Наконец мы реализовали программы тестирования командной строки для отправки тестовых уведомлений для наших клиентского приложения через GCM. 


## <a name="related-links"></a>Связанные ссылки

- [RemoteNotifications GCM (пример)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications)
- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
