---
title: Удаленный уведомления с Firebase облако обмена сообщениями
description: Это пошаговое руководство содержит пошаговое объяснение того, как реализуется с помощью Firebase Cloud Messaging удаленного уведомлений (также называемые push-уведомлений) в приложении Xamarin.Android. Описание для реализации различных классах, которые необходимы для обмена данными с сообщениями Firebase облака (FCM), предоставляет примеры того, как настроить Android манифеста для доступа к FCM и демонстрирует подчиненных обмена сообщениями с использованием Firebase Консоль.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: e2f25504b971a0332dc51dc9b017c9c83222ec57
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044942"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Удаленный уведомления с Firebase облако обмена сообщениями

_Это пошаговое руководство содержит пошаговое объяснение того, как реализуется с помощью Firebase Cloud Messaging удаленного уведомлений (также называемые push-уведомлений) в приложении Xamarin.Android. Описание для реализации различных классах, которые необходимы для обмена данными с сообщениями Firebase облака (FCM), предоставляет примеры того, как настроить Android манифеста для доступа к FCM и демонстрирует подчиненных обмена сообщениями с использованием Firebase Консоль._

## <a name="fcm-notifications-overview"></a>Общие сведения о FCM уведомления

В этом пошаговом руководстве будет вызван базовое приложение **FCMClient** будет создана для иллюстрации essentials FCM обмена сообщениями. **FCMClient** проверяет наличие службы Google Play, получает от FCM токенов регистрации, отображает удаленного уведомления, отправляемые из консоли Firebase и подписывается на сообщения раздела:

[![Снимок экрана примера приложения](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

Мы подробно рассмотрим в следующих разделах:

1.  Фон уведомления

2.  Сообщения раздела

3.  Уведомления переднего плана

В этом пошаговом руководстве будет постепенно добавлена возможность **FCMClient** и запустите его на устройстве или эмуляторе, чтобы понять, как он взаимодействует с FCM. Ведение журнала будет использовать в работающем приложении транзакции с серверами FCM следящий сервер и вы увидите, как уведомления создаются из FCM сообщения, которые вводятся в консоли Firebase уведомления о графическом интерфейсе пользователя. 

## <a name="requirements"></a>Требования

Прежде чем перейти в этом пошаговом руководстве, необходимо получить необходимые учетные данные для использования серверов FCM Google. Этот процесс описан в [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). В частности, необходимо загрузить **google services.json** файл для использования с примерами кода, в данном пошаговом руководстве. Если вы не еще создали проект в консоли Firebase (или если вы еще не загрузили **google services.json** файла), в разделе [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

Для запуска примера приложения, необходимо будет Android тестовое устройство или эмулятор, совместимо с Firebase. Firebase Cloud Messaging поддерживает клиентов, выполняющихся на Android 2.3 (Gingerbread) или более поздней версии, и эти устройства также должен иметь установленное приложение магазина Google Play (Google Play служб 9.2.1 или более поздней версии). Если вы еще не установленное на вашем устройстве приложение магазина Google Play, посетите [Google Play](https://support.google.com/googleplay) веб-сайта, чтобы загрузить и установить его. Кроме того можно использовать пакет SDK для Android эмулятор Android 2.3 или более поздней версии, а не тестовое устройство (не требуется для установки магазина Google Play, если вы используете эмулятор Android SDK). 

## <a name="start-an-app-project"></a>Запуск проекта приложения

Чтобы начать, создайте новый пустой проект Xamarin.Android с именем **FCMClient**. Если вы не знакомы с созданием проектам Xamarin.Android, см. раздел [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md). После создания нового приложения, следующим шагом является задайте имя пакета и установите несколько пакетов NuGet, которые будут использоваться для связи с FCM. 

### <a name="set-the-package-name"></a>Задайте имя пакета

В [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), указано имя пакета для приложения включена FCM. Это имя пакета также служит в качестве *идентификатор приложения* , связанное с ключом API. Настройте приложение для использования этого имени пакета.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Откройте окно свойств **FCMClient** проекта. 

2.  В **Android манифеста** задайте имя пакета. 

В следующем примере имя пакета имеет значение `com.xamarin.fcmexample`: 

[![Задание имени пакета](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

При обновлении **Android манифеста**, также проверьте, убедитесь, что `Internet` разрешены. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1.  Откройте окно свойств **FCMClient** проекта. 

2.  В **приложения Android** задайте имя пакета. 

В следующем примере имя пакета имеет значение `com.xamarin.fcmexample`: 

[![Задание имени пакета](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

При обновлении **Android манифеста**, также проверьте, убедитесь, что `INTERNET` разрешены (под **требуемые разрешения**). 

-----

Обратите внимание, что клиентское приложение не сможет получать маркер регистрации из FCM, если это имя пакета не *точно* соответствует имени пакета, который был введен в консоли Firebase. 

### <a name="add-the-xamarin-google-play-services-base-package"></a>Добавить базовый пакет служб Xamarin Google Play

Поскольку Firebase Cloud Messaging зависит от службы Google Play [Xamarin службы Google Play - Base](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) необходимо добавить пакет NuGet для проекта Xamarin.Android. Вам потребуется версии 29.0.0.2 или более поздней версии.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  В Visual Studio щелкните правой кнопкой мыши **References > Управление пакетами NuGet...** . 

2.  Нажмите кнопку **Обзор** и найдите **Xamarin.GooglePlayServices.Base**. 

3.  Установить этот пакет в **FCMClient** проекта: 

    [![Установка базы служб Google Play](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1.  Щелкните правой кнопкой мыши в Visual Studio для Mac **пакеты > Добавление пакетов...** . 

2.  Поиск **Xamarin.GooglePlayServices.Base**. 

3.  Установить этот пакет в **FCMClient** проекта: 

    [![Установка базы служб Google Play](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Если возникает ошибка во время установки NuGet, закройте **FCMClient** проекта, откройте его снова и повторить попытку установки NuGet. 

При установке **Xamarin.GooglePlayServices.Base**, все необходимые зависимости также будут установлены. Изменить **MainActivity.cs** и добавьте следующее `using` инструкции: 

```csharp
using Android.Gms.Common;
```

Этот оператор позволяет `GoogleApiAvailability` класса в **Xamarin.GooglePlayServices.Base** для **FCMClient** кода. 
`GoogleApiAvailability` используется для проверки наличия службы Google Play. 

### <a name="add-the-xamarin-firebase-messaging-package"></a>Добавить Xamarin Firebase, обмен сообщениями пакета

Для получения сообщений от FCM, [Xamarin Firebase - сообщениями](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) пакета NuGet необходимо добавить в проект приложения. Без этого пакета приложения не может получать сообщения от серверов FCM.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  В Visual Studio щелкните правой кнопкой мыши **References > Управление пакетами NuGet...** . 

2.  Проверьте **включить предварительный выпуск** и выполните поиск **Xamarin.Firebase.Messaging**. 

3.  Установить этот пакет в **FCMClient** проекта: 

    [![Установка Xamarin Firebase обмена сообщениями](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1.  Щелкните правой кнопкой мыши в Visual Studio для Mac **пакеты > Добавление пакетов...** . 

2.  Проверьте **Показать пакеты предварительного выпуска** и выполните поиск **Xamarin.Firebase.Messaging**. 

3.  Установить этот пакет в **FCMClient** проекта: 

    [![Установка Xamarin Firebase обмена сообщениями](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----
 
При установке **Xamarin.Firebase.Messaging**, все необходимые зависимости также будут установлены.

Далее следует изменить **MainActivity.cs** и добавьте следующее `using` инструкции:

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

Первые две инструкции сделайте типы в **Xamarin.Firebase.Messaging** пакет NuGet для **FCMClient** кода. **Android.Util** расширяет его функциональные возможности ведения журнала, который будет использоваться для наблюдения за транзакциями с FMS. 


### <a name="add-the-google-services-json-file"></a>Добавить файл JSON служб Google

Следующим шагом является добавление **google services.json** файл в корневой каталог проекта: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Копировать **google services.json** в папку проекта.

2.  Добавить **google services.json** в проект приложения (щелкните **Показать все файлы** в **обозревателе решений**, щелкните правой кнопкой мыши **google-services.json**, а затем выберите **включить в проект**). 

3.  Выберите **google services.json** в **обозревателе решений** окна.

4.  В **свойства** задайте **действие при построении** для **GoogleServicesJson** (если **GoogleServicesJson** действия сборки не отображается, Сохраните и закройте решение, а затем снова открыть его):

    [![При выборе режима построения GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1.  Копировать **google services.json** в папку проекта.

2.  Добавить **google services.json** в проект приложения. 

3.  Щелкните правой кнопкой мыши **google services.json**.

4.  Задать **действие построения** для **GoogleServicesJson**: 

    [![При выборе режима построения GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)
 
-----
 

Когда **google services.json** добавляется в проект (и **GoogleServicesJson** действия сборки установлено), процесс построения извлекает ключ API и код клиента и добавляет эти учетные данные объединенный / созданный **AndroidManifest.xml** , находящийся в **obj/Debug/android/AndroidManifest.xml**. Процесс слияния автоматически добавляет все разрешения и другие элементы FCM, которые необходимы для подключения к серверам FCM. 


## <a name="check-for-google-play-services"></a>Проверка службы Google Play

Сначала создается исходное расположение элементов пользовательского интерфейса приложения. Изменить **Resources/layout/Main.axml** и замените его содержимое следующим кодом XML: 

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

Это `TextView` будет использоваться для отображения сообщения, указывающие, установлен ли службы Google Play. Сохранить изменения в **Main.axml**. Изменить **MainActivity.cs** и добавьте следующее объявление переменной экземпляра для `MainActivity` класса: 

```csharp
TextView msgText;
```

Google, Майкрософт рекомендует, приложений Android проверки наличия APK воспроизведение служб Google перед обращением к функции службы Google Play (Дополнительные сведения см. в разделе [проверки службы Google Play](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)). В следующем примере `OnCreate` метод убедитесь, что доступен службы Google Play, прежде чем приложение попытается использовать FCM службы. Добавьте следующий метод `MainActivity` класса: 

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

Этот код проверяет устройстве, чтобы определить, установлен ли APK воспроизведение служб Google. Если он не установлен, появится в `TextBox` , указывает, что пользователь для загрузки из магазина Google Play APK (или включить ее в параметрах системы на устройстве). 

Замените метод `OnCreate` следующим кодом: 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);
    IsPlayServicesAvailable ();
}
```

`IsPlayServicesAvailable` вызывается в конце `OnCreate` , чтобы проверить службы Google Play выполняют каждый раз запускается приложение. Если ваше приложение имеет `OnResume` метод, он должен вызывать `IsPlayServicesAvailable` из `OnResume` также. Полностью повторное построение и запуск приложения. Если все настроено правильно, вы увидите экран, который выглядит как следующий снимок экрана: 

[![Приложение указывает, что службы Google Play](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Если этот результат не получен, APK воспроизведение служб Google проверить, установлен ли на устройстве (Дополнительные сведения см. в разделе [параметр копии службы Google Play](https://developers.google.com/android/guides/setup)). Также убедитесь, что вы добавили **Xamarin.Google.Play.Services.Base** пакет для вашей **FCMClient** проекта, как было описано выше.


## <a name="add-the-instance-id-receiver"></a>Добавление приемника идентификатор экземпляра

Следующим шагом является добавление в службу, которая расширяет `FirebaseInstanceIdService` для обработки Создание поворот и обновление Firebase регистрации токенов. (Дополнительные сведения о регистрации токенов см. в разделе [регистрации в FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).) `FirebaseInstanceIdService` Служба необходима для FCM иметь возможность отправлять сообщения на устройстве. Когда `FirebaseInstanceIdService` служба добавляется в клиентское приложение, приложение будет автоматически получать сообщения FCM и отображать их в виде уведомлений, каждый раз, когда backgrounded приложения. 

### <a name="declare-the-receiver-in-the-android-manifest"></a>Объявите получателя в манифесте Android

Изменить **AndroidManifest.xml** и вставьте следующий `<receiver>` элементы в `<application>` раздела: 

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

Этот XML-код выполняет следующие задачи.

-   Объявляет `FirebaseInstanceIdReceiver` реализацию, которая предоставляет [уникальный идентификатор](https://developers.google.com/instance-id/) для каждого экземпляра приложения. Данный получатель также проверяет подлинность и авторизует действия. 

-   Объявляет внутреннюю `FirebaseInstanceIdInternalReceiver` реализацию, которая используется для запуска служб безопасно.

`FirebaseInstanceIdReceiver` — `WakefulBroadcastReceiver` , Получающий `FirebaseInstanceId` и `FirebaseMessaging` событий и доставляет их в класс, производный от `FirebaseInstanceIdService`. 

### <a name="implement-the-firebase-instance-id-service"></a>Реализация службы Firebase идентификатор экземпляра

Выполняет работу по регистрации приложения FCM пользовательский `FirebaseInstanceIdService` службы, который предоставляется. 
`FirebaseInstanceIdService` выполняет следующие действия: 

1.  Использует [API идентификатор экземпляра](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) для формирования токенов безопасности, который разрешает клиентского приложения для доступа к FCM и сервером приложений. Взамен приложение получает регистрации токена из FCM. 

2.  Пересылает маркер регистрации на сервер приложений, если это требуется для сервера приложений.

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

Эта служба реализует `OnTokenRefresh` метод, вызываемый при первоначальном создании или изменении маркер регистрации. Когда `OnTokenRefresh` запускается, он извлекает последние маркера из `FirebaseInstanceId.Instance.Token` свойство (которая обновляется асинхронно FCM). В этом примере маркер обновления записывается в, чтобы его можно просмотреть в окне вывода. 

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` вызывается редко: используется для обновления маркера в следующих случаях: 

-   При установке или удалении приложения.

-   Когда пользователь удаляет данные приложения. 

-   Если приложение удаляет идентификатор экземпляра.

-   При компрометации маркера безопасности.

В соответствии с Google [идентификатор экземпляра](https://developers.google.com/instance-id/guides/android-implementation) документации, идентификатор экземпляра FCM служба отправит запрос на его токен периодически (как правило, каждые 6 месяцев) обновления приложения. 

`OnTokenRefresh` также вызывает `SendRegistrationToAppServer` связываемый регистрации пользователя токена с учетной записью стороне сервера (если таковые имеются), поддерживаемое приложение: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Поскольку эта реализация зависит от структуры приложения сервера, в этом примере предоставляется пустое тело метода. Если приложение сервера необходимо FCM регистрационную информацию, измените `SendRegistrationToAppServer` связываемый с любой учетной записи серверные обслуживается приложение маркер идентификатора экземпляра FCM пользователя. (Обратите внимание, что маркер является непрозрачным для клиентского приложения). 

При отправке на сервер приложений, маркер `SendRegistrationToAppServer` должен поддерживать значение типа boolean для указания, было ли отправлено токен на сервер. Если это логическое выражение имеет значение false, `SendRegistrationToAppServer` отправляет этот токен на сервер приложений &ndash; в противном случае маркер уже был отправлен на сервер приложений в предыдущем вызове. В некоторых случаях (например, это `FCMClient` примере), сервер приложений не требуется маркер; таким образом, этот метод не является обязательным для этого примера. 

## <a name="implement-client-app-code"></a>Реализация клиентского кода приложения

Теперь, когда службы получателя в месте, чтобы воспользоваться преимуществами этих служб можно написать код клиентского приложения. В следующих разделах кнопки добавляется в пользовательский интерфейс для входа маркер регистрации (также называется *маркер идентификатора экземпляра*), и добавляется дополнительный код `MainActivity` для просмотра `Intent` сведения при запуске приложения уведомление: 

[![Токен кнопка журнала, добавленная на экран приложения](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Маркеры журнала

Код, добавленный в этот шаг предназначен только для демонстрационных целей &ndash; производства клиентские приложения будет иметь не требуется вход токенов регистрации. Изменить **Resources/layout/Main.axml** и добавьте следующее `Button` объявление сразу после `TextView` элемента: 

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

Изменить **MainActivity.cs** и добавьте следующее объявление члена `MainActivity` класса:

```csharp
const string TAG = "MainActivity";
```

Добавьте следующий код в конец `OnCreate` метод: 

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

Этот код регистрирует текущий маркер в окне «Вывод» при **журнала маркеров** касании кнопки. 

### <a name="handle-notification-intents"></a>Дескриптор уведомления целей

Когда пользователь касается уведомления, выданного **FCMClient**любых данных, сопровождающие уведомления о сообщение становится доступным в `Intent` дополнения. Изменить **MainActivity.cs** и добавьте следующий код в начало `OnCreate` метод (перед вызовом `IsPlayServicesAvailable`): 

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

Средство запуска приложений `Intent` возникает, когда пользователь касается его сообщение уведомления, этот код будет входить содержащиеся в ней данные `Intent` в окне вывода. Если указан другой `Intent` должно быть запущено, `click_action` поля сообщения уведомления должно быть присвоено, `Intent` (средство запуска `Intent` используется, если нет `click_action` указан). 


## <a name="background-notifications"></a>Фон уведомления

Построение и запуск **FCMClient** приложения. **Журнала маркеров** отображается кнопка:

[![Отображается журнал токен кнопка](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Коснитесь **журнала маркеров** кнопки. В окне вывода IDE появится примерно следующее сообщение об ошибке: 

[![Маркер идентификатора экземпляра, отображаются в окне вывода](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

Помеченные длинную строку **маркера** токен идентификатор экземпляра, вставляемого в консоль Firebase &ndash; выберите и скопируйте строку в буфер обмена. Если вы не видите маркер идентификатора экземпляра, добавьте следующую строку в начало `OnCreate` метод, чтобы убедиться, что **google services.json** правильно проанализировать:

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

`google_app_id` Значение записи в окне вывода должен соответствовать `mobilesdk_app_id` значение записывается в **google services.json**. 

### <a name="send-a-message"></a>Отправить сообщение

Вход в [консоли Firebase](https://console.firebase.google.com), выберите проект, щелкните **уведомления**и нажмите кнопку **отправки первого сообщения**: 

[![Ваш первое сообщение кнопка отправки](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

На **создать сообщение** введите текст сообщения и выберите **одно устройство**. Скопируйте маркер идентификатора экземпляра из окна вывода интегрированной среды разработки и вставьте его в **FCM регистрационный маркер** поле Firebase консоли: 

[![Создать диалоговое окно сообщения](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

На Android устройство (или эмулятор), фоновую приложения, коснувшись Android **Обзор** кнопки и работая непосредственно с начального экрана. Когда устройство будет готово, нажмите кнопку **ОТПРАВКА сообщения** Firebase консоли: 

[![Отправить сообщение кнопки](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Когда **сообщение проверки** отображается диалоговое окно, нажмите кнопку **отправки**.
Значок уведомления появится в области уведомлений на устройства (или эмулятора): 

[![Отображается значок уведомления](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Откройте значок уведомления, чтобы просмотреть сообщение. Сообщение уведомления должно быть ровно то, что был введен в **текст сообщения** поле Firebase консоли: 

[![Сообщение уведомления отображается на устройстве](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Коснитесь значка уведомления, чтобы вернуться к **FCMClient** приложения. `Intent` Дополнения, отправляемые **FCMClient** отображаются в окне вывода интегрированной среды разработки: 

[![Блокировка с намерением дополнения списков из ключа, идентификатор сообщения и ключ свернуть](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

В этом примере **из** ключа задано значение Firebase номер проекта приложения (в данном примере `41590732`) и **collapse_key** равно имени пакета ( **COM.xamarin.fcmexample**). Если вы не получите сообщение, попробуйте удалить **FCMClient** приложения на устройстве (или эмуляторе) и повторите описанные выше шаги. 


> [!NOTE]
> Если закрыть force приложение FCM перестанет доставки уведомлений. Android запрещает фоновой службы широковещательного случайно или без необходимости запуска компонентов приложений остановлена. (Дополнительные сведения об этом поведении см. в разделе [запуск элементов управления на остановленные приложения](https://developer.android.com/about/versions/android-3.1.html#launchcontrols).) По этой причине необходимо вручную удалить приложение при каждом запуске и остановить сеанс отладки &ndash; принудительно FCM, чтобы создать новый токен, чтобы сообщения будут продолжать принимать.

### <a name="add-a-custom-default-notification-icon"></a>Добавить значок по умолчанию уведомлений

В предыдущем примере значок будет присвоено значок приложения. Следующий XML-код настраивает значком по умолчанию для уведомлений. Android отображается этот значок по умолчанию для всех сообщений уведомлений, где значок уведомления не задано явно. 

Чтобы добавить значок по умолчанию уведомления, добавить значок, чтобы **ресурсы или drawable** каталога, изменить **AndroidManifest.xml**и вставьте следующий `<meta-data>` элемент в `<application>`раздела: 

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

В этом примере значок уведомления, находящийся в **ресурсы/drawable/ic\_stat\_ic\_notification.png** будет использоваться как значок уведомления по умолчанию. Если значок по умолчанию не настроен в **AndroidManifest.xml** и значок не задан в полезные данные уведомления, Android использует значок приложения как значок уведомления (как показано на уведомление значок снимке экрана выше). 

## <a name="handle-topic-messages"></a>Обрабатывать сообщения раздела

Код, написанный в данный момент обрабатывает токенов регистрации и расширяет его функциональные возможности удаленного уведомления в приложение. Далее в этом примере добавляется код, который прослушивает *сообщения раздела* и пересылает их пользователю как удаленный уведомления. Раздел сообщения являются FCM, отправляемые одно или несколько устройств, которые подписаны на определенного раздела. Дополнительные сведения о разделе сообщений см. в разделе [обмена сообщениями в разделе](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

### <a name="subscribe-to-a-topic"></a>Подпишитесь на раздел

Изменить **Resources/layout/Main.axml** и добавьте следующее `Button` объявление сразу после предыдущего `Button` элемента:

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Этот XML-код добавляет **подписаться на уведомления** кнопки макета.
Изменить **MainActivity.cs** и добавьте следующий код в конец `OnCreate` метод: 

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Этот код находит **подписаться на уведомления** кнопки в макете и присваивает ее обработчик щелчка код, который вызывает `FirebaseMessaging.Instance.SubscribeToTopic`, передав в подписке разделе _новостей_. Когда пользователь нажимает кнопку **Subscribe** кнопки, приложение подписывается на _новостей_ раздела. В следующем разделе _новостей_ раздел будет отправлено из графического интерфейса Firebase консоли уведомления. 

### <a name="send-a-topic-message"></a>Отправка сообщения раздела

Удалить это приложение, заново собрать его и запустите его еще раз. Нажмите кнопку **подписки на уведомления** кнопки:

[![Подписка на уведомления кнопки](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Если приложение подписка успешно, вы увидите **разделе синхронизация выполнена успешно,** окно вывода в Интегрированной среде разработки: 

[![В окне вывода отображаются сообщения раздела синхронизация выполнена успешно](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Для отправки сообщения раздела, выполните следующие действия:

1.  В консоли Firebase щелкните **НОВОЕ сообщение**. 

2.  На **создать сообщение** введите текст сообщения и выберите **разделе**. 

3.  В **разделе** раскрывающееся меню, выберите раздел встроенных **новостей**: 

    [![При выборе раздела новостей](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  На Android устройство (или эмулятор), фоновую приложения, коснувшись Android **Обзор** кнопки и работая непосредственно с начального экрана. 

5.  Когда устройство будет готово, нажмите кнопку **ОТПРАВКА сообщения** Firebase консоли. 

6.  Проверьте окно вывода интегрированной среды разработки, чтобы увидеть **новости иразделы/** в выходные данные журнала: 

    [![Будет выведено сообщение из /topic/news](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Если это сообщение будет отображаться в окне «Вывод», в области уведомлений на устройстве Android также появляется значок уведомления. Откройте значок уведомления, чтобы просмотреть сообщения раздела: 

[![Раздел появится в виде уведомлений](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Если вы не получите сообщение, попробуйте удалить **FCMClient** приложения на устройстве (или эмуляторе) и повторите описанные выше шаги. 

## <a name="foreground-notifications"></a>Уведомления переднего плана

Чтобы получать уведомления в foregrounded приложений, необходимо реализовать `FirebaseMessagingService`. Эта служба также для получения полезных данных и требуется для отправки сообщений с вышестоящим. Следующие примеры показывают, как реализовать службу, которая расширяет `FirebaseMessagingService` &ndash; итоговое приложение сможет обрабатывать удаленного уведомления, пока она запущена на переднем плане. 

### <a name="implement-firebasemessagingservice"></a>Реализуйте FirebaseMessagingService

`FirebaseMessagingService` Служба включает `OnMessageReceived` метод, который пишется для обработки входящих сообщений. Обратите внимание, что `OnMessageReceived` вызывается для сообщения уведомления *только* когда приложение выполняется на переднем плане. Когда приложение выполняется в фоновом режиме, автоматически созданных уведомлений отображается (как было показано ранее в этом пошаговом руководстве). 

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

Обратите внимание, что `MESSAGING_EVENT` должны объявляться условий фильтра, чтобы новые FCM сообщения направляются на `MyFirebaseMessagingService`: 

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Когда клиентское приложение получает сообщение из FCM, `OnMessageReceived` извлекает содержимое сообщения из переданного `RemoteMessage` , вызывая его `GetNotification` метод. Затем она записывает содержимое сообщения, чтобы его можно просмотреть в окне вывода интегрированной среды разработки: 

```csharp
Log.Debug(TAG, "From: " + message.From);
Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
```

> [!NOTE]
> Если установить точки останова в `FirebaseMessagingService`, ваш сеанс отладки может или не можете достигнуть этих точек останова из-за способа FCM доставляет сообщения.
 

### <a name="send-another-message"></a>Отправить сообщение в другой

Удалить это приложение, заново собрать его, запустите его еще раз и для отправки другого сообщения, выполните следующие действия:

1.  В консоли Firebase щелкните **НОВОЕ сообщение**. 

2.  На **создать сообщение** введите текст сообщения и выберите **одно устройство**. 

3.  Скопируйте строку токена из окна вывода интегрированной среды разработки и вставьте его в **FCM регистрационный маркер** поле Firebase консоли, как и раньше. 

4.  Убедитесь, что приложение выполняется на переднем плане, а затем нажмите кнопку **ОТПРАВКА сообщения** Firebase консоли: 

    [![Отправка другое сообщение, отправленное из консоли](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  Когда **сообщение проверки** отображается диалоговое окно, нажмите кнопку **отправки**.

6.  Входящее сообщение регистрируется в окне вывода интегрированной среды разработки.

    [![Текст сообщения напечатаны в окне вывода](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notifications-sender"></a>Добавление локального уведомления отправителя

В этом примере оставшиеся входящее сообщение FCM будет преобразован в локальный уведомление, которое запускается, пока приложение выполняется на переднем плане. Изменить **MyFirebaseMessageService.cs** и добавьте следующее `using` инструкции: 

```csharp
using FCMClient;
using System.Collections.Generic;
```

Добавьте следующий метод `MyFirebaseMessagingService`: 

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (string key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }
    var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

    var notificationBuilder = new Notification.Builder(this)
        .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
        .SetContentTitle("FCM Message")
        .SetContentText(messageBody)
        .SetAutoCancel(true)
        .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManager.FromContext(this);
    notificationManager.Notify(0, notificationBuilder.Build());
}
```

Чтобы отличить это уведомление из фона уведомления, этот код помечает уведомления со значком, отличается от applicatiion значок. Добавить [ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) для **ресурсы или drawable** и включить ее в **FCMClient** проекта. 

`SendNotification` Использует метод `Notification.Builder` для создания уведомлений, и `NotificationManager` используется для запуска уведомления. Уведомление содержит `PendingIntent` это даст возможность пользователю откройте приложение и просмотреть содержимое строки, переданной в `messageBody`. В зависимости от версии Android, которую планируется использовать с вашим приложением, может потребоваться использовать `NotificationCompat.Builder` вместо него. Дополнительные сведения о `Notification.Builder`, в разделе [локального уведомления](~/android/app-fundamentals/notifications/local-notifications.md). 

Вызовите `SendNotification` метод в конце `OnMessageReceived` метод: 

```csharp
SendNotification(message.GetNotification().Body, message.Data);
```

В результате этих изменений `SendNotification` будет выполняться при получении уведомления, пока приложение находится на переднем плане, а уведомление будет отображаться в области уведомлений. Если приложение backgrounded, `SendNotification` не запустится, и вместо этого будет запущен фоновый уведомление (приведенных ранее в этом руководстве). 

### <a name="send-the-last-message"></a>Отправить последнее сообщение

Удалить это приложение, заново собрать его, запустите его еще раз, а затем выполните следующие действия для отправки последнего сообщения:

1.  В консоли Firebase щелкните **НОВОЕ сообщение**. 

2.  На **создать сообщение** введите текст сообщения и выберите **одно устройство**. 

3.  Скопируйте строку токена из окна вывода интегрированной среды разработки и вставьте его в **FCM регистрационный маркер** поле Firebase консоли, как и раньше. 

4.  Убедитесь, что приложение выполняется на переднем плане, а затем нажмите кнопку **ОТПРАВКА сообщения** Firebase консоли: 

    [![Отправка сообщения переднего плана](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

На этот раз сообщение, которое было зарегистрировано в окне вывода также упаковывается в новое уведомление &ndash; значка области уведомлений появляется в области уведомлений, пока приложение выполняется на переднем плане: 

[![Значок уведомления для сообщения переднего плана](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

При открытии уведомления, вы увидите, что последнее сообщение, отправленное из графического интерфейса Firebase консоли уведомления: 

[![Передний план уведомлений отображается со значком переднего плана](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)


## <a name="disconnecting-from-fcm"></a>Отключение от FCM

Чтобы отменить подписку на раздел, вызовите [UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29) метод [FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging) класса. Например, чтобы отказаться от подписки _новостей_ разделе Подписка на более ранних версиях **Unsubscribe** кнопка может быть добавлен к макету, с помощью следующего кода обработчика:

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

Чтобы отменить регистрацию устройства в полном FCM, удалить идентификатор экземпляра, вызвав [DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29) метод [FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) класса. Пример:

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

Вызов этого метода удаляет идентификатор экземпляра и связанные с ним данные. В результате прекращается периодической отправки FCM данных на устройстве.

 
## <a name="troubleshooting"></a>Устранение неполадок

Следующие описания проблемы и способы решения проблем, которые могут возникнуть при использовании Xamarin.Android Firebase Cloud Messaging.

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp не инициализирован

В некоторых случаях может появиться следующее сообщение об ошибке:

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Это известная проблема, можно обойти, очистка решения и повторного построения проекта (**сборки > Очистить решение**, **сборки > Перестроить решение**). Дополнительные сведения см. в этой [форум обсуждений](https://forums.xamarin.com/discussion/96263/default-firebaseapp-is-not-initialized-in-this-process).


## <a name="summary"></a>Сводка

В этом пошаговом руководстве подробные шаги по реализации в приложении Xamarin.Android Firebase Cloud Messaging удаленного уведомления. Оно описано, как установить необходимые пакеты, необходимые для обмена данными FCM и его объясняется настройка Android манифеста для доступа к серверам FCM. Он предоставляется пример кода, демонстрирующий способы проверки наличия службы Google Play. Демонстрирует реализацию службы прослушивателя идентификатор экземпляра, согласовывает с FCM маркер регистрации и объясняется, как этот код создает уведомления в фоновом режиме, пока backgrounded приложения. Оно описано как подписаться на сообщения раздела, и он предоставляется пример реализации службы прослушивания сообщений, которая используется для получения и отображения удаленного уведомления, пока приложение выполняется на переднем плане. 


## <a name="related-links"></a>Связанные ссылки

- [FCMNotifications (пример)](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Обмен сообщениями Firebase Cloud](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
