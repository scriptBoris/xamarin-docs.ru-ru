---
title: Удаленные уведомления с помощью Firebase Cloud Messaging
description: Это пошаговое руководство содержит пошаговое объяснение способа использования Firebase Cloud Messaging для реализации удаленных уведомлений (также называемые Push-уведомления) в приложениях Xamarin.Android. Он показывает, как реализовать различные классы, которые необходимы для обмена данными с помощью Firebase Cloud Messaging (FCM), приводятся примеры того, как настроить манифест Android для доступа к FCM и демонстрирует подчиненных обмена сообщениями с помощью Firebase Консоль.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: de0e2c5ff10de9136c4cb5987c80ce22c7b18c4d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105549"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Удаленные уведомления с помощью Firebase Cloud Messaging

_Это пошаговое руководство содержит пошаговое объяснение способа использования Firebase Cloud Messaging для реализации удаленных уведомлений (также называемые Push-уведомления) в приложениях Xamarin.Android. Он показывает, как реализовать различные классы, которые необходимы для обмена данными с помощью Firebase Cloud Messaging (FCM), приводятся примеры того, как настроить манифест Android для доступа к FCM и демонстрирует подчиненных обмена сообщениями с помощью Firebase Консоль._

## <a name="fcm-notifications-overview"></a>Общие сведения о уведомления FCM

В этом пошаговом руководстве под названием базовое приложение **FCMClient** будет создан для демонстрации essentials FCM обмена сообщениями. **FCMClient** проверяет наличие служб Google Play, получает маркеры регистрации из FCM, отображает удаленных уведомлений, отправляемых в консоли Firebase и подписывается на раздел сообщений:

[![Снимок экрана с примером приложения](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

Мы подробно рассмотрим в следующих разделах:

1.  Уведомления в фоновом режиме

2.  Сообщения раздела

3.  Уведомления переднего плана

В этом пошаговом руководстве вы постепенно добавляются функции **FCMClient** и запустите его на устройстве или в эмуляторе, чтобы понять, как она взаимодействует с FCM. Вы будете использовать ведение журнала для следящих серверов транзакции живого приложения с серверами FCM, и вы увидите, как уведомления, создаваемые сообщениях FCM, введенные в графическом интерфейсе пользователя уведомления консоли Firebase.

## <a name="requirements"></a>Требования


Это будет полезно ознакомиться с [различных типов сообщений](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) , может быть отправлено службой Firebase Cloud Messaging. Полезные данные сообщения определит, как клиентское приложение получит и обработает сообщение.

Прежде чем вы сможете продолжить в этом пошаговом руководстве, необходимо получить необходимые учетные данные для использования Google FCM серверов; Этот процесс описан в [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm).
В частности, необходимо загрузить **google-services.json** файл для использования с примерами кода, представленные в этом пошаговом руководстве. Если вы создали проект в консоли Firebase (или если вы еще не загрузили **google-services.json** файл), см. в разделе [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

Чтобы запустить пример приложения, необходимо будет Android тестовое устройство или эмулятор, совместимый с Firebase. Firebase Cloud Messaging поддерживает клиентов, работающих на Android 4.0 или более поздней версии, и эти устройства также должно быть установлено приложение Google Play Store (Google Play Services 9.2.1 или более поздней). Если у вас еще нет на Google Play Store приложение, установленное на вашем устройстве, посетите [Google Play](https://support.google.com/googleplay) веб-сайта, чтобы загрузить и установить его. В качестве альтернативы можно использовать эмулятор SDK для Android Google Play установленных службах вместо тестовое устройство (у вас нет Установка Google Play Store, если вы используете эмулятор пакета SDK для Android).

## <a name="start-an-app-project"></a>Запуск проекта приложения

Чтобы начать, необходимо создать новый пустой проект Xamarin.Android с именем **FCMClient**. Если вы не знакомы с созданием проектов Xamarin.Android, см. в разделе [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md).
После создания нового приложения, необходимо будет задать имя пакета и установке необходимых пакетов NuGet, которые будут использоваться для связи с FCM.

### <a name="set-the-package-name"></a>Задайте имя пакета

В [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), было задано имя пакета для приложения с поддержкой FCM. Это имя пакета также служит в качестве [ *идентификатор приложения* ](./firebase-cloud-messaging.md#fcm-in-action-app-id) , связанный с [ключ API](firebase-cloud-messaging.md#fcm-in-action-api-key). Настройте приложение, чтобы использовать это имя пакета:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Откройте свойства **FCMClient** проекта.

2.  В **манифест Android** задайте имя пакета.

В следующем примере имя пакета имеет значение `com.xamarin.fcmexample`:

[![Задание имени пакета](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

При обновлении **манифест Android**, также проверьте, убедитесь, что `Internet` разрешены.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1.  Откройте свойства **FCMClient** проекта.

2.  В **приложения Android** задайте имя пакета.

В следующем примере имя пакета имеет значение `com.xamarin.fcmexample`:

[![Задание имени пакета](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

При обновлении **манифест Android**, также проверьте, убедитесь, что `INTERNET` разрешены (в разделе **необходимые разрешения**).

-----

> [!IMPORTANT]
> Клиентское приложение не сможет получить маркер регистрации из FCM, если это имя пакета не *точно* соответствует имени пакета, который был введен в консоли Firebase.

### <a name="add-the-xamarin-google-play-services-base-package"></a>Добавьте пакет Xamarin Google Play Services базы

Поскольку Firebase Cloud Messaging зависит от службы Google Play [Xamarin сервисы Google Play - Base](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) необходимо добавить пакет NuGet в проект Xamarin.Android. Вам потребуется версия 29.0.0.2 или более поздней версии.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  В Visual Studio щелкните правой кнопкой мыши **ссылки > Управление пакетами NuGet...** .

2.  Нажмите кнопку **Обзор** вкладку и выполните поиск **Xamarin.GooglePlayServices.Base**.

3.  Установить этот пакет в **FCMClient** проекта:

    [![Установка базовых служб Google Play](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1.  В Visual Studio для Mac, щелкните правой кнопкой мыши **пакеты > Добавить пакеты...** .

2.  Поиск **Xamarin.GooglePlayServices.Base**.

3.  Установить этот пакет в **FCMClient** проекта:

    [![Установка базовых служб Google Play](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Если возникает ошибка во время установки пакета NuGet, закройте **FCMClient** проекта, откройте его снова и снова попытайтесь установить NuGet.

При установке **Xamarin.GooglePlayServices.Base**, все необходимые зависимости также устанавливаются. Изменить **MainActivity.cs** и добавьте следующие `using` инструкции:

```csharp
using Android.Gms.Common;
```

Эта инструкция делает `GoogleApiAvailability` в класс **Xamarin.GooglePlayServices.Base** для **FCMClient** кода.
`GoogleApiAvailability` используется для проверки на наличие служб Google Play.

### <a name="add-the-xamarin-firebase-messaging-package"></a>Добавьте пакет Xamarin обмен сообщениями Firebase

Для получения сообщений от FCM, [Xamarin Firebase - Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) необходимо добавить пакет NuGet в проект приложения. Без этого пакета приложения Android не может принимать сообщения от серверов FCM.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  В Visual Studio щелкните правой кнопкой мыши **ссылки > Управление пакетами NuGet...** .

2. Поиск **Xamarin.Firebase.Messaging**.

3.  Установить этот пакет в **FCMClient** проекта:

    [![Установка Xamarin Firebase обмена сообщениями](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1.  В Visual Studio для Mac, щелкните правой кнопкой мыши **пакеты > Добавить пакеты...** .

2.  Проверьте **Показать пакеты предварительного выпуска** и выполните поиск **Xamarin.Firebase.Messaging**.

3.  Установить этот пакет в **FCMClient** проекта:

    [![Установка Xamarin Firebase обмена сообщениями](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

При установке **Xamarin.Firebase.Messaging**, все необходимые зависимости также устанавливаются.

Далее следует изменить **MainActivity.cs** и добавьте следующие `using` инструкции:

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

Первые две инструкции сделать типы в **Xamarin.Firebase.Messaging** пакет NuGet для **FCMClient** кода. **Android.Util** расширяет функциональные возможности ведения журнала, который будет использоваться для наблюдения за транзакции с FMS.

### <a name="add-googleplayservices-json"></a>Добавление Google Services JSON-файл

Следующим шагом является добавление **google-services.json** файл в корневой каталог проекта:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Копировать **google-services.json** в папку проекта.

2.  Добавить **google-services.json** в проект приложения (щелкните **Показать все файлы** в **обозревателе решений**, щелкните правой кнопкой мыши **google-services.json**, а затем выберите **включить в проект**).

3.  Выберите **google-services.json** в **обозревателе решений** окна.

4.  В **свойства** установите **действие при построении** для **GoogleServicesJson**:

    [![При выборе режима построения GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > Если **GoogleServicesJson** действие сборки не указывается, сохраните и закройте решение, а затем снова открыть его.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1.  Копировать **google-services.json** в папку проекта.

2.  Добавить **google-services.json** в проект приложения.

3.  Щелкните правой кнопкой мыши **google-services.json**.

4.  Задайте **действие при сборке** для **GoogleServicesJson**:

    [![При выборе режима построения GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

Когда **google-services.json** добавляется в проект (и **GoogleServicesJson** задано действие сборки), процесс построения извлекает идентификатор клиента и [ключ API](./firebase-cloud-messaging.md#fcm-in-action-api-key) и затем Добавляет этих учетных данных в слияние/генерируемый **AndroidManifest.xml** , находящийся в **obj/Debug/android/AndroidManifest.xml**. Процесс слияния автоматически добавляет все разрешения и другие элементы FCM, которые необходимы для подключения к серверам FCM.


## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>Проверки службы Google Play и создания канала уведомлений с помощью

Google рекомендует соблюдать, что приложения Android для проверки наличия пакета APK служб Google Play перед обращением к функции службы Google Play (Дополнительные сведения см. в разделе [проверять службы Google Play](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)).

Сначала будет создана исходное расположение элементов пользовательского интерфейса приложения. Изменить **Resources/layout/Main.axml** и замените его содержимое следующим кодом XML:

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

Это `TextView` будет использоваться для отображения сообщения, указывающие, установлен ли сервисы Google Play. Сохраните изменения в **Main.axml**.


Изменить **MainActivity.cs** и добавьте следующие переменные экземпляра `MainActivity` класса:

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

Переменные `CHANNEL_ID` и `NOTIFICATION_ID` будет использоваться в методе [ `CreateNotificationChannel` ](#create-notification-channel-code) , будут добавлены к `MainActivity` далее в этом пошаговом руководстве.


В следующем примере `OnCreate` метод будет проверять службы Google Play доступен, прежде чем приложение пытается использовать службы FCM.
Добавьте следующий метод в `MainActivity` класса:

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
            msgText.Text = "This device is not supported";
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

Этот код проверяет устройства и проверьте, установлен ли APK служб Google Play. Если он не установлен, сообщение отображается в `TextBox` которая дает пользователю загрузить пакет APK в Google Play Store (или включить ее в настройках устройства системы).

<a name="create-notification-channel-code"></a>Приложениями, запущенными в Android 8.0 (уровень API 26) или более поздней версии необходимо создать [ _канала уведомления_ ](~/android/app-fundamentals/notifications/local-notifications.md) для публикации их уведомления.  Добавьте следующий метод в `MainActivity` класс, который создает канал уведомления (при необходимости):

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channel = new NotificationChannel(CHANNEL_ID,
                                          "FCM Notifications",
                                          NotificationImportance.Default)
                  {

                      Description = "Firebase Cloud Messages appear in this channel"
                  };

    var notificationManager = (NotificationManager)GetSystemService(Android.Content.Context.NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Замените метод `OnCreate` следующим кодом:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();

    CreateNotificationChannel();
}
```

`IsPlayServicesAvailable` вызывается в конце `OnCreate` таким образом, чтобы проверять службы Google Play выполняется при каждом запуске приложения. Метод `CreateNotificationChannel` вызывается для проверки наличия канал уведомления для устройств под управлением Android 8 или более поздней версии. Если приложение имеет `OnResume` метод, он должен вызывать `IsPlayServicesAvailable` из `OnResume` также. Полностью перестроить и запустить приложение. Если все настроено правильно, вы увидите экран, который выглядит примерно так:

[![Приложение указывает, что сервисы Google Play](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Если полученный в результате проверка установки APK служб Google Play на устройстве (Дополнительные сведения см. в разделе [параметр вверх сервисы Google Play](https://developers.google.com/android/guides/setup)).
Также убедитесь, что вы добавили **Xamarin.Google.Play.Services.Base** пакета вашего **FCMClient** проекта, как описано ранее.


## <a name="add-the-instance-id-receiver"></a>Добавить получателя идентификатор экземпляра

Следующим шагом является добавление услуга, которая расширяет `FirebaseInstanceIdService` для создания, поворота, обработки и обновления [маркеры регистрации Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token). `FirebaseInstanceIdService` Служба необходима для FCM иметь возможность отправлять сообщения на устройство. Когда `FirebaseInstanceIdService` служба добавляется в клиентское приложение, приложение будет автоматически получать сообщения FCM и отображать их в виде уведомления, каждый раз, когда приложение выполняется в фоновом режиме.

### <a name="declare-the-receiver-in-the-android-manifest"></a>Объявите получателя в манифест Android

Изменить **AndroidManifest.xml** и вставьте следующий `<receiver>` элементы в `<application>` разделе:

```xml
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
    android:exported="false" />
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
</receiver>
```

Этот XML-код выполняет следующие функции:

-   Объявляет `FirebaseInstanceIdReceiver` реализация, которая предоставляет [уникальный идентификатор](https://developers.google.com/instance-id/) для каждого экземпляра приложения. Также данный получатель проверяет подлинность и авторизует действия.

-   Объявляет внутреннего `FirebaseInstanceIdInternalReceiver` реализацию, используемую для безопасного запуска служб.

-   [Идентификатор приложения](./firebase-cloud-messaging.md#fcm-in-action-app-id) хранится в **google-services.json** файл, который был [добавлен в проект](#add-googleplayservices-json). Заменяет токен привязки Xamarin.Android Firebase `${applicationId}` с Идентификатором приложения; не требуется дополнительный код с клиентского приложения для предоставления идентификатор приложения.

`FirebaseInstanceIdReceiver` — `WakefulBroadcastReceiver` , Получающий `FirebaseInstanceId` и `FirebaseMessaging` событий и предоставляет их класса, производного от `FirebaseInstanceIdService`.

### <a name="implement-the-firebase-instance-id-service"></a>Реализация службы идентификатора экземпляра Firebase

Работу по регистрации приложения для использования FCM обрабатывается пользовательский `FirebaseInstanceIdService` службы, предоставляемой.
`FirebaseInstanceIdService` выполняет следующие действия:

1.  Использует [API Идентификаторов экземпляра](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) для формирования токенов безопасности, который разрешает клиентское приложение, чтобы получить доступ к FCM и сервера приложений. В ответ приложение получает обратно [маркер регистрации](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token) из FCM.

2.  Перенаправляет токен регистрации для сервера приложений, если это требуется для сервера приложений.

Добавьте новый файл с именем **MyFirebaseIIDService.cs** и замените его код шаблона следующим:

```csharp
using System;
using Android.App;
using Firebase.Iid;
using Android.Util;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    {
        const string TAG = "MyFirebaseIIDService";
        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }
        void SendRegistrationToServer(string token)
        {
            // Add custom implementation, as needed.
        }
    }
}
```

Эта служба реализует `OnTokenRefresh` метод, вызываемый при первоначальном создании или изменении маркер регистрации. Когда `OnTokenRefresh` выполняется, он извлекает последний токен из `FirebaseInstanceId.Instance.Token` свойство (которое асинхронно обновляется средствами FCM). В этом примере обновленный маркер регистрируются, чтобы его можно просмотреть в окне вывода:

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` вызывается редко: она используется для обновления маркера в следующих случаях:

-   Когда приложение будет установлено или удалено.

-   Когда пользователь удаляет данные приложения.

-   Когда приложение удаляет идентификатор экземпляра

-   При компрометации маркера безопасности.

В соответствии с Google [идентификатор экземпляра](https://developers.google.com/instance-id/guides/android-implementation) документации, служба идентификатора экземпляра FCM будет запрашивать его токен периодически (обычно каждые 6 месяцев) обновления приложения.

`OnTokenRefresh` также вызывает `SendRegistrationToAppServer` связываемый регистрации пользователя токена с учетной записью на сервере (если таковые имеются), поддерживаемое приложение:

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Поскольку эта реализация зависит от структуры приложения сервера, в этом примере предоставляется пустое тело метода. Если ваш сервер приложений требует сведений о регистрации в FCM, измените `SendRegistrationToAppServer` должен быть сопоставлен любую учетную запись на сервере, обслуживается приложение маркер идентификатора экземпляра FCM пользователя. (Обратите внимание, что маркер является непрозрачным для клиентского приложения).

Когда маркер отправляется для сервера приложений, `SendRegistrationToAppServer` следует поддерживать логическое значение, указывающее был ли маркер отправлен на сервер. Если это логическое выражение имеет значение false, `SendRegistrationToAppServer` отправляет маркер для сервера приложений &ndash; в противном случае маркер уже было отправлено на сервер приложения в предыдущем вызове метода. В некоторых случаях (например, это `FCMClient` примере), сервера приложений не требуется маркер; таким образом, этот метод не является обязательным для этого примера.

## <a name="implement-client-app-code"></a>Реализация клиентского кода приложения

Теперь, когда получатель службы расположены на месте, чтобы воспользоваться преимуществами этих служб могут записываться код клиента приложения. В следующих разделах, также добавляется кнопка для пользовательского интерфейса для входа маркер регистрации (также называется *маркер идентификатора экземпляра*), и добавляется дополнительный код `MainActivity` для просмотра `Intent` сведения при запуске приложения из уведомление:

[![Для экрана приложения добавлена кнопка токен журнала](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Маркеры журнала

Код, добавленный в этот шаг предназначен только для демонстрационных целей &ndash; рабочего приложения клиента бы нужды в журнал маркеры регистрации. Изменить **Resources/layout/Main.axml** и добавьте следующие `Button` объявление сразу после `TextView` элемент:

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

Добавьте в конце метода `MainActivity.OnCreate` приведенный ниже код:

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

Этот код выводит текущий маркер в окно вывода при **журнала маркеров** нажатии кнопки.

### <a name="handle-notification-intents"></a>Дескриптор уведомления намерений

Когда пользователь касается уведомления, выданного **FCMClient**, любые данные, сопровождающие этого уведомления сообщение становится доступным в `Intent` дополнения. Изменить **MainActivity.cs** и добавьте следующий код в верхнюю часть `OnCreate` метод (перед вызовом `IsPlayServicesAvailable`):

```csharp
if (Intent.Extras != null)
{
    foreach (var key in Intent.Extras.KeySet())
    {
        var value = Intent.Extras.GetString(key);
        Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
    }
}
```

Средство запуска приложений `Intent` возникает, когда пользователь касается его сообщение уведомления, поэтому этот код добавляет в журнал содержащиеся в ней данные в `Intent` в окне вывода. Если указан другой `Intent` должен сработать, `click_action` поля сообщения уведомления должно быть присвоено, `Intent` (средство запуска `Intent` используется, если аргумент `click_action` указан).


## <a name="background-notifications"></a>Уведомления в фоновом режиме

Сборка и запуск **FCMClient** приложения. **Журнала маркеров** отображается кнопка ":

[![Отображается маркер кнопка журнала](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Коснитесь **журнала маркеров** кнопки. В окне вывода IDE отображаться примерно следующее сообщение:

[![Маркер идентификатора экземпляра, отображаются в окне вывода](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

Длинная строка с названием **маркера** является маркером идентификатора экземпляра, который будет вставлен в консоли Firebase &ndash; выберите и скопируйте эту строку в буфер обмена. Если вы не видите маркер идентификатора экземпляра, добавьте следующую строку в начало `OnCreate` метод, чтобы убедиться, что **google-services.json** синтаксический анализ выполнен правильно:

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

`google_app_id` Значение записи в окне вывода должно соответствовать `mobilesdk_app_id` зарегистрированное значение в **google-services.json**.

### <a name="send-a-message"></a>Отправить сообщение

Войдите в [консоли Firebase](https://console.firebase.google.com), выберите проект, щелкните **уведомления**и нажмите кнопку **отправки первого сообщения**:

[![Отправьте свое первое сообщение кнопки](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

На **создания сообщения** странице введите текст сообщения и выберите **одно устройство**. Скопируйте маркер идентификатор экземпляра из окна вывода интегрированной среды разработки и вставьте его в **маркера регистрации в FCM** поле в консоли Firebase:

[![Диалоговое окно сообщения Compose](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

На Android устройства (или эмуляторе), фоновая приложения, коснувшись Android **Обзор** кнопку и изменения начального экрана. Когда устройство будет готово, нажмите **ОТПРАВКА сообщения** в консоли Firebase:

[![Отправить сообщение кнопки](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Когда **сообщение проверки** диалоговое окно отображается, щелкните **отправки**.
Значок уведомления должны отображаться в области уведомлений устройства (или эмулятора):

[![Отображается значок уведомления](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Откройте значок уведомления, чтобы просмотреть сообщение. Сообщение уведомления, вполне введенному в **текст сообщения** поле в консоли Firebase:

[![Сообщение уведомления отображается на устройстве](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Значок уведомления, чтобы запустить **FCMClient** приложения. `Intent` Дополнения, отправляемые **FCMClient** , перечислены в окне вывода интегрированной среды разработки:

[![Блокировка намерения дополнения списки из ключа, идентификатор сообщения и ключ свернуть](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

В этом примере **из** ключ присваивается номер проекта Firebase приложения (в этом примере `41590732`) и **collapse_key** присваивается имени пакета ( **COM.xamarin.fcmexample**).
Если вы не получите сообщение, попробуйте удалить **FCMClient** приложения на устройстве (или эмуляторе) и повторите описанные выше шаги.


> [!NOTE]
> Если закрыть force приложение FCM перестанет доставки уведомлений. Android запрещает широковещательных рассылок службы фона случайно или без необходимости запуска компонентов приложений остановлена. (Дополнительные сведения об этом поведении см. в разделе [запуска элементы управления, на остановленные приложения](https://developer.android.com/about/versions/android-3.1.html#launchcontrols).) По этой причине необходимо вручную удалить приложение при каждом запустить и остановить сеанс отладки &ndash; FCM для создания нового маркера, чтобы сообщения по-прежнему должны быть получены в результате.

### <a name="add-a-custom-default-notification-icon"></a>Добавление значка уведомления по умолчанию

Значок уведомления в предыдущем примере, имеет значение значка приложения. Следующий XML настраивает пользовательского значка для уведомлений. Android отображается этот значок по умолчанию для всех сообщений уведомлений, где значок уведомления не задано явно.

Чтобы добавить значок уведомлений по умолчанию, добавьте значок таким образом, чтобы **ресурсы/drawable** directory, изменить **AndroidManifest.xml**и вставьте следующий `<meta-data>` элемент в коллекцию `<application>`раздел:

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

В этом примере значок уведомления, находится в **ресурсы/drawable/ic\_stat\_ic\_notification.png** будет использоваться в качестве значка уведомления по умолчанию. Если значок по умолчанию не настроен в **AndroidManifest.xml** и значок не задается в полезные данные уведомления, Android использует значок приложения как значок уведомления (как показано на снимке выше показан значок уведомления).

## <a name="handle-topic-messages"></a>Обработка сообщений раздела

Код, написанный таким образом намного обрабатывает маркеры регистрации и расширяет функциональные возможности удаленного уведомления в приложение. В следующем примере добавляется код, который прослушивает *сообщения раздела* и пересылает их пользователю как удаленные уведомления. Сообщения раздела являются FCM, отправляемые одним или несколькими устройствами, которые подписаны на определенный раздел. Дополнительные сведения о разделе сообщений, см. в разделе [обмена сообщениями в разделе](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

### <a name="subscribe-to-a-topic"></a>Подпишитесь на тему

Изменить **Resources/layout/Main.axml** и добавьте следующие `Button` объявление сразу после предыдущего `Button` элемент:

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Этот XML-кода добавляет **подписаться на уведомления** кнопки в макет.
Изменить **MainActivity.cs** и добавьте следующий код в конец `OnCreate` метод:

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Этот код находит **подписаться на уведомления** кнопки в макете и присваивает ее обработчик щелчка код, который вызывает `FirebaseMessaging.Instance.SubscribeToTopic`, передав в разделе подписанных _новостей_. Когда пользователь касается **Subscribe** кнопки приложение подписывается на _новостей_ раздела. В следующем разделе _новостей_ из графического интерфейса уведомления консоли Firebase будут отправляться сообщения раздела.

### <a name="send-a-topic-message"></a>Отправить сообщение-раздел

Удалить это приложение, заново собрать его и запустите его снова. Нажмите кнопку **подписки на уведомления** кнопки:

[![Подпишитесь на уведомления кнопки](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Если приложение подписана успешно, вы увидите **разделе синхронизация выполнена успешно,** окно вывода в интегрированной среде разработки:

[![Окно вывода отображает сообщение разделе синхронизация выполнена успешно](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Следуйте инструкциям ниже для отправки сообщения раздела:

1.  В консоли Firebase щелкните **НОВОЕ сообщение**.

2.  На **создания сообщения** странице введите текст сообщения и выберите **разделе**.

3.  В **разделе** раскрывающееся меню, выберите раздел встроенных **новостей**:

    [![Выбрав в разделе новостей](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  На Android устройства (или эмуляторе), фоновая приложения, коснувшись Android **Обзор** кнопку и изменения начального экрана.

5.  Когда устройство будет готово, нажмите **ОТПРАВКА сообщения** в консоли Firebase.

6.  Проверьте окно вывода интегрированной среды разработки, чтобы увидеть **/разделов и новостей** выходные данные журнала:

    [![Будет выведено сообщение из /topic/news](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Если это сообщение будет отображаться в окне вывода, значок должен появиться в области уведомлений на устройстве Android. Откройте значок уведомления, чтобы просмотреть сообщения раздела:

[![Раздел сообщение отображается в виде уведомлений](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Если вы не получите сообщение, попробуйте удалить **FCMClient** приложения на устройстве (или эмуляторе) и повторите описанные выше шаги.

## <a name="foreground-notifications"></a>Уведомления переднего плана

Чтобы получать уведомления в приложениях foregrounded, необходимо реализовать `FirebaseMessagingService`. Эта служба необходима также для получения полезных данных, а также для отправки вышестоящего сообщений. В следующих примерах показано, для реализации службы, который расширяет `FirebaseMessagingService` &ndash; результирующего приложения будут иметь возможность обрабатывать удаленные уведомления, пока оно выполняется на переднем плане.

### <a name="implement-firebasemessagingservice"></a>Реализовать FirebaseMessagingService

`FirebaseMessagingService` Служба отвечает за получение и обработку сообщений от Firebase. Каждое приложение должно подкласс этого типа и переопределение `OnMessageReceived` для обработки входящего сообщения. Когда приложение находится на переднем плане `OnMessageReceived` обратного вызова всегда будет обрабатывать сообщения.

> [!NOTE]
> Приложения имеют только 10 секунд, в которой для обработки входящего сообщения Firebase Cloud. Все, что больше времени, чем это должны быть запланированы на фонового выполнения, такие как с помощью библиотеки [Android планировщик заданий](~/android/platform/android-job-scheduler.md) или [диспетчер заданий Firebase](~/android/platform/firebase-job-dispatcher.md).

Добавьте новый файл с именем **MyFirebaseMessagingService.cs** и замените его код шаблона следующим:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Media;
using Android.Util;
using Firebase.Messaging;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    {
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        }
    }
}
```

Обратите внимание, что `MESSAGING_EVENT` фильтром намерений должен быть объявлен, таким образом, чтобы новый FCM сообщения будут направляться `MyFirebaseMessagingService`:

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Когда клиентское приложение получает сообщение из FCM, `OnMessageReceived` извлекает содержимое сообщения из переданного `RemoteMessage` , вызвав его `GetNotification` метод. Затем она записывает содержимое сообщения, чтобы его можно просмотреть в окне вывода интегрированной среды разработки:

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> Если вы установите точки останова в `FirebaseMessagingService`, ваш сеанс отладки может или не столкнуться с этими точками останова из-за как FCM доставляет сообщения.


### <a name="send-another-message"></a>Отправьте еще одно сообщение

Удалить это приложение, заново собрать его, запустите его снова и выполните следующие действия для отправки другого сообщения:

1.  В консоли Firebase щелкните **НОВОЕ сообщение**.

2.  На **создания сообщения** странице введите текст сообщения и выберите **одно устройство**.

3.  Скопируйте строку маркера из окна вывода интегрированной среды разработки и вставьте его в **маркера регистрации в FCM** поле консоли Firebase, как и раньше.

4.  Убедитесь, что приложение выполняется на переднем плане, а затем щелкните **ОТПРАВКА сообщения** в консоли Firebase:

    [![Послав другое сообщение из консоли](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  Когда **сообщение проверки** диалоговое окно отображается, щелкните **отправки**.

6.  Входящее сообщение регистрируется в окно вывода в интегрированной среде разработки.

    [![Текст сообщения, напечатаны в окне вывода](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notification-sender"></a>Добавление локального уведомления отправителя

В этом примере оставшиеся входящее сообщение FCM, преобразуются в локальное уведомление, которое запускается, пока приложение выполняется на переднем плане. Изменить **MyFirebaseMessageService.cs** и добавьте следующие `using` инструкции:

```csharp
using FCMClient;
using System.Collections.Generic;
```

Добавьте следующий метод в `MyFirebaseMessagingService`:

<a name="sendnotification-method"></a>
```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (var key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }

    var pendingIntent = PendingIntent.GetActivity(this,
                                                  MainActivity.NOTIFICATION_ID,
                                                  intent,
                                                  PendingIntentFlags.OneShot);

    var notificationBuilder = new  NotificationCompat.Builder(this, MainActivity.CHANNEL_ID)
                              .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                              .SetContentTitle("FCM Message")
                              .SetContentText(messageBody)
                              .SetAutoCancel(true)
                              .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(MainActivity.NOTIFICATION_ID, notificationBuilder.Build());
}
```

Чтобы отличить это уведомление из уведомлений в фоновом режиме, этот код помечает уведомления со значком, отличается от значка приложения. Добавьте файл [ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) для **ресурсы/drawable** и включить ее в **FCMClient** проекта .

`SendNotification` Использует метод ` NotificationCompat.Builder` Чтобы создать уведомление, и `NotificationManagerCompat` служит для запуска уведомления. Уведомление содержит `PendingIntent` , позволит пользователю открыть приложение и просмотреть содержимое строка, переданная в `messageBody`. Дополнительные сведения о `NotificationCompat.Builder`, см. в разделе [локальных уведомлений](~/android/app-fundamentals/notifications/local-notifications.md).

Вызовите `SendNotification` метод в конце `OnMessageReceived` метод:

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

В результате этих изменений `SendNotification` будет запускаться при каждом получении уведомления, пока приложение находится на переднем плане, а уведомление будет отображаться в области уведомлений.

Когда приложение находится в фоновом режиме, [полезные данные сообщения](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) определит, как обрабатывается сообщение:

* **Уведомление** &ndash; сообщения будут отправляться **области уведомлений**. Локальное уведомление, будут отображаться. Приложение запустится, когда пользователь касается уведомления.
* **Данные** &ndash; сообщения будут обрабатываться `OnMessageReceived`.
* **Оба** &ndash; сообщения, полезные данные уведомления и данных, которые будут предоставляться на панель задач. При запуске приложения, полезные данные будут отображаться в `Extras` из `Intent` которая использовалась для запуска приложения.

В этом примере, если приложение выполняется в фоновом режиме `SendNotification` будет выполняться, если для получения полезных данных сообщения. В противном случае будет запускаться уведомление фона (показано ранее в этом пошаговом руководстве).

### <a name="send-the-last-message"></a>Отправить последнее сообщение

Удалить это приложение, заново собрать его, запустите его снова, а затем следуйте инструкциям ниже для отправки последнее сообщение:

1.  В консоли Firebase щелкните **НОВОЕ сообщение**.

2.  На **создания сообщения** странице введите текст сообщения и выберите **одно устройство**.

3.  Скопируйте строку маркера из окна вывода интегрированной среды разработки и вставьте его в **маркера регистрации в FCM** поле консоли Firebase, как и раньше.

4.  Убедитесь, что приложение выполняется на переднем плане, а затем щелкните **ОТПРАВКА сообщения** в консоли Firebase:

    [![Отправка сообщения переднего плана](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

На этот раз сообщение, которое было записано в окне вывода также упаковывается в новое уведомление &ndash; в области уведомлений появится значок уведомления, пока приложение выполняется на переднем плане:

[![Значок уведомления для сообщения переднего плана](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

При открытии уведомления, вы должны увидеть последнее сообщение, отправленное из консоли Firebase уведомления графического интерфейса пользователя:

[![Уведомление переднего плана, отображается со значком переднего плана](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)


## <a name="disconnecting-from-fcm"></a>Отключение от FCM

Чтобы отменить подписку на раздел, вызовите [UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29) метод [FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging) класса. Например, чтобы отменить подписку на _новостей_ разделе подписаны на более ранних версиях **Unsubscribe** кнопка может быть добавлен к следующим кодом обработчик макета:

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

Чтобы отменить регистрацию устройства в полностью FCM, удалить идентификатор экземпляра, вызвав [DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29) метод [идентификатора экземпляра Firebase](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) класса. Пример:

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

Вызов этого метода удаляет идентификатор экземпляра и данные, связанные с ним. В результате прекращается периодической отправки FCM данных на устройство.


## <a name="troubleshooting"></a>Устранение неполадок

Следующие описания проблемы и обходные пути, которые могут возникнуть при использовании Firebase Cloud Messaging с помощью Xamarin.Android.

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp не инициализирован

В некоторых случаях может появиться следующее сообщение об ошибке:

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Это известная проблема, можно обойти, очистка решения и повторного построения проекта (**сборки > Очистить решение**, **сборки > Перестроить решение**). Дополнительные сведения см. в этом [обсуждения в форумах](https://forums.xamarin.com/discussion/96263/default-firebaseapp-is-not-initialized-in-this-process).


## <a name="summary"></a>Сводка

В этом пошаговом руководстве подробные шаги по реализации Firebase Cloud Messaging удаленные уведомления в приложениях Xamarin.Android. Оно было описано, как установить необходимые пакеты, необходимые для обмена данными FCM, и оно было рассмотрено, как настроить манифест Android для доступа к серверам FCM. Она предоставляла пример кода, иллюстрирующий для обнаружения служб Google Play. Вы узнали, как реализовать службу прослушивания Идентификаторов экземпляра, которая согласовывает с FCM, маркер регистрации, и описано, как этот код создает уведомления в фоновом режиме, пока приложение выполняется в фоновом режиме. Оно было рассмотрено, как подписаться на сообщения раздела, и его предоставленный пример реализации службы прослушивателя сообщений, которая используется для получения и отображения удаленные уведомления, пока приложение выполняется на переднем плане.


## <a name="related-links"></a>Связанные ссылки

- [FCMNotifications (пример)](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Обмен сообщениями Firebase Cloud](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [Сведения о сообщениях FCM](https://firebase.google.com/docs/cloud-messaging/concept-options)
