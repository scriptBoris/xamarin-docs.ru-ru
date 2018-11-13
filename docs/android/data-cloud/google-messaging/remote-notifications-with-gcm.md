---
title: Удаленные уведомления с помощью Google Cloud Messaging
description: Это пошаговое руководство содержит пошаговое объяснение того, как использовать Google Cloud Messaging для реализации удаленных уведомлений (также называемые Push-уведомления) в приложениях Xamarin.Android. Здесь описываются различные классы, которые необходимо реализовать для обмена данными с Google Cloud Messaging (GCM), также содержатся сведения о задании разрешений в манифесте Android для доступа к службе GCM, а он демонстрирует end-to-end обмена сообщениями с программой тестового примера.
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: be96683a2e63ed802169543dcee55a3431e42130
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528810"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Удаленные уведомления с помощью Google Cloud Messaging

_Это пошаговое руководство содержит пошаговое объяснение того, как использовать Google Cloud Messaging для реализации удаленных уведомлений (также называемые Push-уведомления) в приложениях Xamarin.Android. Здесь описываются различные классы, которые необходимо реализовать для обмена данными с Google Cloud Messaging (GCM), также содержатся сведения о задании разрешений в манифесте Android для доступа к службе GCM, а он демонстрирует end-to-end обмена сообщениями с программой тестового примера._

> [!NOTE]
> GCM была заменена [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM сервер и клиентские API-интерфейсы [стали нерекомендуемыми](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) и больше не будут доступны как можно скорее 11 апреля 2019 г.

## <a name="gcm-notifications-overview"></a>Общие сведения о уведомления GCM

В этом пошаговом руководстве мы создадим приложение Xamarin.Android, которое использует Google Cloud Messaging (GCM) для реализации удаленных уведомлений (также известный как *Push-уведомления*). Мы реализуем различные службы намерения и прослушиватель, использующие GCM для удаленного обмена сообщениями, и мы протестируем нашей реализации с программой командной строки, которая имитирует сервер приложений. 

Обратите внимание, что Firebase Cloud Messaging (FCM) — это новая версия GCM &ndash; Google рекомендует FCM, а не GCM. Если вы используете GCM, рекомендуется обновление до FCM. Дополнительные сведения о FCM, см. в разделе [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

Прежде чем вы сможете продолжить в этом пошаговом руководстве, необходимо получить необходимые учетные данные, используемые серверы GCM от Google; Этот процесс описан в [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md). В частности, необходимо будет *ключ API* и *кода отправителя* для вставки в примере код, представленный в этом пошаговом руководстве. 

Мы будем использовать следующие действия для создания клиентского приложения GCM с поддержкой Xamarin.Android:

1.  Установите дополнительные пакеты, необходимые для обмена данными с серверами GCM.
2.  Настройка разрешений приложения для доступа к серверам GCM.
3.  Реализуйте код для проверки на наличие служб Google Play. 
4.  Реализуйте службу намерений регистрации, которая согласовывает с GCM для маркера регистрации.
5.  Реализуйте службу прослушивания Идентификаторов экземпляра, который прослушивает изменения токена регистрации из GCM.
6.  Реализуйте службу прослушивания GCM, получающий удаленных сообщений с сервера приложений через GCM.

Это приложение будет использовать новую функцию GCM, известный как *обмена сообщениями в разделе*. В разделе обмена сообщениями, сервера приложений отправляет сообщение в раздел, а не список отдельных устройств. Устройства, которые подписаны на этот раздел может получать сообщения раздела как Push-уведомлений. Дополнительные сведения об обмене сообщениями через раздел GCM, см. в разделе Google [реализации обмена сообщениями разделе](https://developers.google.com/cloud-messaging/topic-messaging). 

Когда клиентское приложение будет готово, мы реализуем командной строки C# приложение, которое отправляет Push-уведомления на уровне приложения клиента с помощью GCM. 

## <a name="walkthrough"></a>Пошаговое руководство

Чтобы начать, давайте создадим новое пустое решение вызывается **RemoteNotifications**. Далее добавим в это решение на основе проекта Android **приложение Android** шаблона. Давайте назовем этот проект **ClientApp**. (Если вы не умеете создавать проекты Xamarin.Android, см. в разделе [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md).) **ClientApp** проект будет содержать код для клиентского приложения Xamarin.Android, получающий удаленные уведомления с помощью GCM. 

### <a name="add-required-packages"></a>Добавьте необходимые пакеты

Прежде чем мы можем реализовать наш код клиента приложения, необходимо установить несколько пакетов, которые мы будем использовать для связи с GCM. Кроме того мы необходимо добавить приложение Google Play Store устройство, если они еще не установлены.

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>Добавьте пакет Xamarin Google Play Services GCM

Для получения сообщений из Google Cloud Messaging, [сервисы Google Play](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/) framework должен присутствовать на устройстве. Без этой платформы приложение Android не может принимать сообщения от серверов GCM. Сервисы Google Play выполняется в фоновом режиме при включении устройства Android, незаметно прослушивание сообщений из GCM. При поступлении этих сообщений, сервисы Google Play преобразовывает сообщения в объекты Intent и затем передает эти intents к приложениям, которые зарегистрированы для них. 

В Visual Studio щелкните правой кнопкой мыши **ссылки > Управление пакетами NuGet...** ; в Visual Studio для Mac, щелкните правой кнопкой мыши **пакеты > Добавить пакеты...** . Поиск **Xamarin сервисы Google Play - GCM** и установить этот пакет в **ClientApp** проекта: 

[![Установка служб Google Play](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

При установке **Xamarin сервисы Google Play - GCM**, **Xamarin сервисы Google Play - Base** устанавливается автоматически. Если отобразится сообщение об ошибке, измените проект *минимум Android к целевому объекту* параметр в значение, отличное от **компиляция с помощью пакета SDK версии** и повторите попытку установки NuGet. 

Далее следует изменить **MainActivity.cs** и добавьте следующие `using` инструкции:

```csharp
using Android.Gms.Common;
using Android.Util;
```

Это делает типы в пакете GMS-службы Google Play для нашего кода, а также добавляет функции ведения журнала, который будет использоваться для отслеживания операций с GMS. 

#### <a name="google-play-store"></a>Google Play Store

Для получения сообщений из GCM, Google Play Store приложения должны устанавливаться на устройстве. (Каждый раз, когда на устройстве установлено приложение Google Play, Google Play Store также устанавливается, поэтому вполне вероятно, что он уже установлен на тестовом устройстве). Без Google Play приложение Android не может принимать сообщения из GCM. Если у вас еще нет на Google Play Store приложение, установленное на вашем устройстве, посетите [Google Play](https://support.google.com/googleplay) веб-сайта, чтобы загрузить и установить Google Play. 

Кроме того можно использовать эмулятор Android под управлением Android 2.2 или более поздней версии, а не на тестовое устройство (у вас нет для установки на эмуляторе Android Google Play Store). Тем не менее если вы используете эмулятор, необходимо использовать Wi-Fi для подключения к службе GCM и необходимо открыть определенные порты в брандмауэре Wi-Fi, как описано далее в этом пошаговом руководстве. 

### <a name="set-the-package-name"></a>Задайте имя пакета

В [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md), мы указали имя пакета для нашего приложения с поддержкой GCM (это имя пакета также служит в качестве *идентификатор приложения* связанный ключ API и идентификатор отправителя). Откроем свойства **ClientApp** проекта и задайте имя пакета для данной строки. В этом примере задается имя пакета `com.xamarin.gcmexample`:

[![Задание имени пакета](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

Обратите внимание, что клиентское приложение сможет получать маркер регистрации из GCM, если это имя пакета не *точно* совпадает с именем пакета, мы ввели в консоль разработчика Google. 

### <a name="add-permissions-to-the-android-manifest"></a>Добавление разрешений в манифест Android

Приложение Android необходимо иметь следующие разрешения настроены, прежде чем он может получать уведомления от Google Cloud Messaging: 

-   `com.google.android.c2dm.permission.RECEIVE` &ndash; Предоставляет разрешение на уровне приложения для регистрации и получения сообщений из Google Cloud Messaging. (Что делает `c2dm` означает? Это означает _облака для обмена сообщениями устройства_, которой была создана неподдерживаемые до GCM. 
    По-прежнему использует GCM `c2dm` во многих строк его разрешений.) 

-   `android.permission.WAKE_LOCK` &ndash; (Необязательно) Устройство ЦП не переходить в спящий режим при прослушивании для сообщения. 

-   `android.permission.INTERNET` &ndash; Предоставляет доступ к Интернету, чтобы клиентское приложение могло взаимодействовать с GCM. 

-   *имя_пакета* `.permission.C2D_MESSAGE` &ndash; регистрирует приложение в Android и запрашивает разрешение на исключительно получать все C2D сообщений (облака на устройство). *Имя_пакета* префикс — так же, как идентификатор вашего приложения. 

Мы настроим эти разрешения в манифесте Android. Изменим то **AndroidManifest.xml** и замените его содержимое следующим кодом XML: 

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

В приведенном выше XML, измените *YOUR_PACKAGE_NAME* имени пакета для проекта клиентского приложения. Например, `com.xamarin.gcmexample`. 

### <a name="check-for-google-play-services"></a>Проверка службы Google Play

В этом пошаговом руководстве мы создаем приложение Минималистическая с одним `TextView` в пользовательском Интерфейсе. Это приложение не указано, что непосредственно взаимодействие с GCM. Вместо этого мы будет отслеживать в окне вывода, чтобы увидеть как наши приложения подтверждений в GCM, и мы проверим область уведомлений для новых уведомлений при их поступлении. 

Во-первых давайте создадим макет для области сообщений. Изменить **Resources.layout.Main.axml** и замените его содержимое следующим кодом XML: 

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

При запуске клиентского приложения, мы хотим его, чтобы проверить, что сервисы Google Play доступен, прежде чем мы попытаемся связаться GCM. Изменить **MainActivity.cs** и замените ``count`` экземпляра объявление переменной со следующим объявлением переменной экземпляра: 

```csharp
TextView msgText;
```

Добавьте следующий метод в **MainActivity** класса: 

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

Этот код проверяет устройства и проверьте, установлен ли APK служб Google Play. Если он не установлен, сообщение отображается в области сообщений, который указывает, что пользователю загрузить пакет APK в Google Play Store (или включить эту функцию в настройках устройства системы). Поскольку нам нужно выполнять эту проверку при запуске клиентского приложения, мы добавим вызов этого метода в конце `OnCreate`. 


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

Этот код проверяет наличие пакета APK служб Google Play и записывает результаты в области сообщений. 

Давайте полностью перестроить и запустить приложение. Вы увидите экран, который выглядит примерно так: 

[![Сервисы Google Play доступен](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

Если полученный в результате проверка установки APK служб Google Play на устройстве и который **Xamarin сервисы Google Play - GCM** пакет будет добавлен к вашей **ClientApp** проекта, как описано ранее. Если возникнет ошибка сборки, очистки решения и сборку проекта еще раз. 

Далее мы напишем код, обратитесь в службу GCM и получать маркер регистрации.

### <a name="register-with-gcm"></a>Регистрации в GCM

Прежде чем приложение сможет получать удаленные уведомления с сервера приложений, его регистрации в GCM и получить маркер регистрации. Обрабатывается работу по регистрации в GCM наше приложение `IntentService` , мы создаем. Наши `IntentService` выполняет следующие действия: 

1.  Использует [InstanceID](https://developers.google.com/instance-id/) API для формирования токенов безопасности, который разрешает наше приложение клиента для доступа к серверу приложений. Взамен мы получаем регистрации токена из GCM.

2.  Перенаправляет токен регистрации для сервера приложений (если сервер приложений требует его).

3.  Подписывается на один или несколько каналов раздел уведомлений.

После реализовать это `IntentService`, мы протестируем его, чтобы увидеть, если мы получаем регистрации токена из GCM.

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

1.  Войдите на [консоли Google Cloud](https://console.cloud.google.com/) и выберите имя проекта в раскрывающемся меню. В **проекта info** области, отображаемый для проекта, выберите **перейдите к параметрам проекта**:

    [![Выбор проекта XamarinGCM](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2.  На **параметры** найдите **номер проекта** &ndash; это идентификатор отправителя для проекта:

    [![Отображаемый номер проекта](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

Нам нужно запустить наш `RegistrationIntentService` при запуске нашего приложения. Изменить **MainActivity.cs** и изменение `OnCreate` метод, чтобы наши `RegistrationIntentService` запускается после проверяется на наличие служб Google Play: 

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

Во-первых, мы заметки к нашей `RegistrationIntentService` атрибутом ниже, чтобы указать, что наша служба не для создания экземпляра системы: 

```csharp
[Service (Exported = false)]
```

`RegistrationIntentService` Конструктор имена рабочий поток *RegistrationIntentService* упростить отладку. 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

Основные функциональные возможности `RegistrationIntentService` находится в `OnHandleIntent` метод. Давайте подробно рассмотрим этот код, чтобы увидеть, как наше приложение регистрацию в GCM.


##### <a name="request-a-registration-token"></a>Запрос маркера регистрации

`OnHandleIntent` сначала вызывает Google [InstanceID.GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;) метод для запроса маркера регистрации из GCM. Мы поместить этот код в `lock` Чтобы защититься от возможности несколько способов регистрации, которые выполняются одновременно &ndash; `lock` гарантирует, что эти intents обрабатываются последовательно. Если мы не удалось получить маркер регистрации, возникает исключение, и мы в журнал ошибку. Если регистрация выполняется успешно, `token` задан маркер регистрации, мы вернулись из GCM: 

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

##### <a name="forward-the-registration-token-to-the-app-server"></a>Пересылать маркер регистрации для сервера приложений

Если мы получаем маркер регистрации (то есть исключение не создано), мы вызываем `SendRegistrationToAppServer` связываемый регистрации пользователя токена с учетной записью на сервере (если таковые имеются), обслуживаемый нашего приложения. Поскольку эта реализация зависит от структуры приложения сервера, пустой метод представлено ниже: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

В некоторых случаях сервера приложений не требуется маркер регистрации пользователя; в этом случае этот метод может опускаться. При отправке маркера регистрации сервера приложений, `SendRegistrationToAppServer` следует поддерживать логическое значение, указывающее был ли маркер отправлен на сервер. Если это логическое выражение имеет значение false, `SendRegistrationToAppServer` отправляет маркер для сервера приложений &ndash; в противном случае маркер уже было отправлено на сервер приложения в предыдущем вызове метода. 


##### <a name="subscribe-to-the-notification-topic"></a>Подписаться на тему уведомления

Затем мы вызываем наших `Subscribe` метод для указания в GCM, которую необходимо подписаться на раздел уведомлений. В `Subscribe`, мы вызываем [GcmPubSub.Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) API подписаться наше приложение клиента ко всем сообщениям, в разделе `/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

Сервера приложений необходимо отправить уведомления `/topics/global` , чтобы получать их. Обратите внимание, что имя раздела в разделе `/topics` можно изменить в зависимости, до тех пор, пока сервер приложений, так и с помощью клиентского приложения согласовать эти имена. (Здесь мы выбрали имя `global` для указания, что мы хотим получать сообщения на все разделы, поддерживаемые сервером приложения.) 

Сведения о разделе GCM, обмен сообщениями на стороне сервера, см. в разделе Google [отправки сообщений на разделы](https://developers.google.com/cloud-messaging/topic-messaging). 


#### <a name="implement-an-instance-id-listener-service"></a>Реализовать службу прослушивания идентификатор экземпляра

Маркеры регистрации являются уникальным и безопасными; Тем не менее клиентское приложение (или GCM) может потребоваться обновить маркер регистрации в случае повторной установки приложения или проблема с безопасностью. По этой причине нужно реализовать `InstanceIdListenerService` , отвечает на запросы токена обновления от GCM. 

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

Добавление заметок `InstanceIdListenerService` с указанным ниже атрибутом, чтобы указать, что служба находится в том, не для создания экземпляра в системе и что он может получить маркер регистрации в GCM (также называется *идентификатор экземпляра*) запросов: 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

`OnTokenRefresh` Метод наших начинает `RegistrationIntentService` таким образом, чтобы его можно перехватить новый маркер регистрации.


#### <a name="test-registration-with-gcm"></a>Проверка регистрации в GCM

Давайте полностью перестроить и запустить приложение. Если вы успешно получили маркер регистрации из GCM, маркер регистрации должны отображаться в окне вывода. Пример: 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>Дескриптор подчиненных сообщений 

Код, который мы реализовали в данный момент — только код «настройки»; он проверяет, если сервисы Google Play установлено и проводит согласование с GCM и сервера приложений, для подготовки к нашей клиентское приложение для получения удаленных уведомлений. Тем не менее у нас есть еще реализовать код, который фактически получает и обрабатывает сообщения подчиненных уведомления. Для этого нужно реализовать *служба прослушивания GCM*. Такая служба получает сообщения раздела с сервера приложений и локально передает их в виде уведомлений. После мы реализуем эту службу, мы создадим тестовую программу для отправки сообщений в GCM, таким образом, мы видим, если наша реализация работает правильно. 


#### <a name="add-a-notification-icon"></a>Добавление значка уведомления

Сначала добавим мелкого значка, который будет отображаться в области уведомлений при запуске наших уведомлений. Вы можете скопировать [этот значок](remote-notifications-with-gcm-images/ic-stat-ic-notification.png) в проект или создайте собственный пользовательский значок. Мы назовем файл значка **ic_stat_button_click.png** и скопируйте его **ресурсы/drawable** папки. Не забывайте использовать **Добавить > существующий элемент...**  разместит этот файл значка в проекте.


#### <a name="implement-a-gcm-listener-service"></a>Реализовать службу прослушивания GCM

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

Давайте рассмотрим каждый раздел наших `GcmListenerService` для понимания принципов его работы. 

Во-первых, мы аннотировать `GcmListenerService` с атрибут, указывающий, что эта служба является не быть создан системой, и мы приводим намерений фильтра, чтобы указать, что он получает сообщения GCM: 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

Когда `GcmListenerService` получает сообщение из GCM, `OnMessageReceived` вызывается метод. Этот метод извлекает содержимое сообщения из переданного `Bundle`, регистрирует содержимое сообщения (поэтому его можно просмотреть в окне вывода) и вызывает метод `SendNotification` для запуска локального уведомления с содержимым полученное сообщение: 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

`SendNotification` Использует метод `Notification.Builder` для создания уведомления, а затем использует `NotificationManager` для запуска уведомления. Фактически это преобразует сообщение удаленного уведомления в локальное уведомление, которые будут отображаться для пользователя.
Дополнительные сведения об использовании `Notification.Builder` и `NotificationManager`, см. в разделе [локальных уведомлений](~/android/app-fundamentals/notifications/local-notifications.md).


#### <a name="declare-the-receiver-in-the-manifest"></a>Объявите получателя в манифесте

Прежде чем мы может получать сообщения из GCM, мы должны объявлять прослушивателя GCM в манифесте Android. Изменим то **AndroidManifest.xml** и замените `<application>` раздел с следующий код XML: 

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

В приведенном выше XML, измените *YOUR_PACKAGE_NAME* имени пакета для проекта клиентского приложения. В нашем примере Пошаговое руководство, имя пакета — `com.xamarin.gcmexample`. 

Давайте взглянем на назначение каждого из параметров в этот XML-код:

|Параметр|Описание|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|Объявляет, что наше приложение реализует получатель GCM, который перехватывает и обрабатывает входящие сообщения push-уведомлений.|
|`com.google.android.c2dm.permission.SEND`|Объявляет, что только серверы GCM может отправлять сообщения непосредственно в приложение.|
|`com.google.android.c2dm.intent.RECEIVE`|Фильтр намерений рекламу, что наше приложение обрабатывает широковещательные сообщения от GCM.|
|`com.google.android.c2dm.intent.REGISTRATION`|Фильтр намерений, рекламу, что наше приложение обрабатывает новых инструментов intents регистрации (то есть мы реализовали службу прослушивания Идентификаторов экземпляра).|

Кроме того, вы можете дополнить `GcmListenerService` с этих атрибутов, а не указывая их в XML; здесь мы указываем их в **AndroidManifest.xml** таким образом, проще воспринимать, примеры кода. 


### <a name="create-a-message-sender-to-test-the-app"></a>Создание отправителя сообщения для тестирования приложения

Давайте добавим C# рабочего стола консольного приложения проекта в решение и назовите его **MessageSender**. Мы будем использовать это консольное приложение для имитации серверу приложений &ndash; будут отправляться сообщения уведомления для **ClientApp** через GCM. 


#### <a name="add-the-jsonnet-package"></a>Добавьте пакет Json.NET

Это консольное приложение мы создаем, полезные данные JSON, содержащий сообщение уведомления, которые нужно отправить в клиентское приложение. Мы будем использовать **Json.NET** упаковать в **MessageSender** для упрощения построения объект JSON, необходимый для GCM. В Visual Studio щелкните правой кнопкой мыши **ссылки > Управление пакетами NuGet...** ; в Visual Studio для Mac, щелкните правой кнопкой мыши **пакеты > Добавить пакеты...** . 

Выполним поиск **Json.NET** пакет и установите его в проект: 

[![Установка пакета Json.NET](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)


#### <a name="add-a-reference-to-systemnethttp"></a>Добавьте ссылку на System.Net.Http

Нам также нужно будет добавить ссылку на `System.Net.Http` таким образом, можно создать экземпляр `HttpClient` для отправки сообщений теста GCM. В **MessageSender** проекта, щелкните правой кнопкой мыши **ссылки > Добавить ссылку** и прокрутите вниз, пока не увидите **System.Net.Http**. Установите флажок рядом с полем **System.Net.Http** и нажмите кнопку **ОК**. 


#### <a name="implement-code-that-sends-a-test-message"></a>Реализовать код, который отправляет тестовое сообщение

В **MessageSender**, изменить **Program.cs** и замените его содержимое следующим кодом:

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

В приведенном выше коде измените *YOUR_API_KEY* на ключ API для проекта клиентского приложения. 

Этот сервер приложения тестирования отправляет приведенное ниже формате JSON в GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM, в свою очередь, передает это сообщение, чтобы клиентское приложение. Давайте создадим **MessageSender** и откройте окно консоли, где мы его могут запустить из командной строки.



### <a name="try-it"></a>Попробуйте!

Теперь мы готовы протестировать наше приложение клиента. Если вы используете эмулятор, или если устройство обменивается с GCM Wi-Fi, необходимо открыть следующие TCP-порты в брандмауэре сообщений GCM, позволяющих протащить: 5228 5229 и 5230: создание.

Запустите клиентское приложение и просмотрите окно вывода. После `RegistrationIntentService` успешно получает регистрации токена из GCM, в окне вывода отобразится маркер с выходными данными журнала, аналогичный следующему:

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

На этом этапе клиентское приложение готова получать сообщение удаленного уведомления. В командной строке выполните **MessageSender.exe** программы, чтобы отправить сообщение «Hello, Xamarin» уведомления в клиентское приложение.
Если вы еще не построены **MessageSender** проекта, сделайте это сейчас.

Для запуска **MessageSender.exe** в Visual Studio, откройте командную строку, измените **MessageSender/bin/Debug** каталог и выполните команду напрямую:

```cmd
MessageSender.exe
```

Для запуска **MessageSender.exe** в Visual Studio для Mac откройте сеанс терминала, измените **MessageSender/bin/Debug** моно использовать для запуска и каталог **MessageSender.exe** 

```bash
mono MessageSender.exe
```

Он может занять до минуты для сообщения для распространения через GCM и обратно вниз, чтобы клиентское приложение. Если сообщение успешно получено, мы должны увидеть результат, аналогичный следующему в окне вывода. 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

Кроме того можно заметить, что в области уведомлений отображается значок нового уведомления: 

[![Значок уведомления отображается на устройстве](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

При открытии в область уведомлений для просмотра уведомлений, вы увидите, что наши удаленного уведомления:

[![Отображается сообщение уведомления](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

Поздравляем, ваше приложение получило его первого удаленного уведомления.

Обратите внимание на то, что GCM сообщения больше не будут приниматься, если приложение является force остановлено. Чтобы возобновить уведомления после принудительно остановите, приложение необходимо вручную перезапустить. Дополнительные сведения об этой политике Android см. в разделе [запуска элементы управления, на остановленные приложения](https://developer.android.com/about/versions/android-3.1.html#launchcontrols) и это [post переполнения стека](http://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267). 

 
## <a name="summary"></a>Сводка

В этом пошаговом руководстве подробные шаги по реализации удаленные уведомления в приложениях Xamarin.Android. Оно было описано, как установить дополнительные пакеты, необходимые для связи с GCM, а его было рассмотрено, как настроить приложение разрешения для доступа к серверам GCM. Она предоставляла пример кода, демонстрирующий, как проверить наличие служб Google Play, как реализовать службу прослушивания Идентификаторов экземпляра, которая согласовывает с, GCM для маркера регистрации и намерением службы регистрации и как реализовать прослушиватель GCM Служба, которая получает и обрабатывает сообщения удаленного уведомления. Наконец мы реализовали программы тестирования командной строки для отправки тестовых уведомлений на уровне приложения клиента через GCM. 


## <a name="related-links"></a>Связанные ссылки

- [RemoteNotifications GCM (пример)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications)
- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
