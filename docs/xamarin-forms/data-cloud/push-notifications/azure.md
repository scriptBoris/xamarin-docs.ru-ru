---
title: Отправка Push-уведомлений из мобильных приложений Azure
description: В этой статье объясняется, как использовать центры уведомлений Azure для отправки Push-уведомлений из экземпляра мобильных приложений Azure для приложения Xamarin.Forms.
ms.prod: xamarin
ms.assetid: A1EF400F-73F4-43E9-A0C3-1569A0F34A3B
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 42ec5ddb6846ccf733f2bf18812f43e7afd45f34
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058582"
---
# <a name="sending-push-notifications-from-azure-mobile-apps"></a>Отправка Push-уведомлений из мобильных приложений Azure

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)

_Концентраторы уведомлений Azure предоставляют масштабируемые Push-инфраструктуру для отправки мобильные Push-уведомления из любой серверной части на любую мобильную платформу, устраняя сложности серверной части, необходимость связываться с разных систем уведомлений платформы. В этой статье объясняется, как использовать центры уведомлений Azure для отправки Push-уведомлений из экземпляра мобильных приложений Azure для приложения Xamarin.Forms._

> [!VIDEO https://youtube.com/embed/le2lDY22xwM]

**Azure передает в центр уведомлений и Xamarin.Forms, [Xamarin University](https://university.xamarin.com/)**

Push-уведомления используются для доставки сведения, такие как сообщения, из серверной системы, приложения на мобильном устройстве, чтобы увеличить приложения вовлеченности и использования. Уведомления могут отправляться в любое время, даже в том случае, если пользователь не использует активно целевому приложению.

Серверные системы отправки Push-уведомлений на мобильные устройства через систем отправки уведомлений платформы (PNS), как показано на следующей схеме:

[![](azure-images/pns.png "Системы отправки уведомлений платформы")](azure-images/pns-large.png#lightbox "систем отправки уведомлений платформы")

Для отправки Push-уведомление, Серверная система обращается к PNS платформы для отправки уведомления на экземпляр приложения клиента. Это значительно увеличивает сложность серверной части при кросс платформенные Push-уведомления являются обязательными, так как необходимо использовать серверную часть каждой платформы PNS API и протокола.

Центры уведомлений Azure устранить сложности, учитывая деталей разных систем уведомлений платформы, позволяя кросс платформенные уведомления отправляться с помощью одного вызова API, как показано на следующей схеме:

[![](azure-images/notification-hub.png)](azure-images/notification-hub-large.png#lightbox)

Для отправки Push-уведомление, серверной части системы только контакты в центре уведомлений Azure, который в свою очередь, взаимодействует с разных систем уведомлений платформы, поэтому Уменьшение сложности серверной части кода, отправляет Push-уведомлений.

Мобильные приложения Azure имеют встроенную поддержку Push-уведомлений с помощью центров уведомлений. Процесс для отправки Push-уведомление из экземпляра мобильных приложений Azure в приложения Xamarin.Forms выглядит следующим образом:

1. Приложение Xamarin.Forms регистрирует в PNS, которая возвращает дескриптор.
1. Экземпляр мобильных приложений Azure отправляет уведомление в свой центр уведомлений Azure, задающего дескриптор устройства, для которых будет включен.
1. В центре уведомлений Azure отправляет уведомления в соответствующую службу pns для устройства.
1. Служба PNS отправляет уведомление на устройство.
1. Приложение Xamarin.Forms обрабатывает уведомление и отображает его.

Пример приложения демонстрирует приложение списка задач, данные которых хранятся в экземпляре мобильных приложений Azure. Каждый раз при добавлении нового элемента к экземпляру мобильных приложений Azure, Push-уведомление отправляется в приложение Xamarin.Forms. На следующих снимках экрана показано каждой платформы, Отображение полученного Push-уведомления:

[![](azure-images/screenshots.png "Пример приложения, получения Push-уведомление")](azure-images/screenshots-large.png#lightbox "пример приложение, получающее Push-уведомление")

Дополнительные сведения о концентраторах уведомлений Azure, см. в разделе [центров уведомлений Azure](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/) и [Добавление Push-уведомлений в приложение Xamarin.Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push/).

## <a name="azure-and-platform-notification-system-setup"></a>Azure и настройка системы уведомления платформы

Процесс интеграции центра уведомлений Azure в экземпляр мобильных приложений Azure выглядит следующим образом:

1. Создайте экземпляр мобильных приложений Azure. Дополнительные сведения см. в разделе [использование мобильное приложение Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Настройка концентратора уведомлений. Дополнительные сведения см. в разделе [Настройка концентратора уведомлений](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#create-hub).
1. Обновление экземпляра мобильных приложений Azure для отправки Push-уведомлений. Дополнительные сведения см. в разделе [обновление серверного проекта для отправки Push-уведомления](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#update-the-server-project-to-send-push-notifications).
1. Зарегистрируйте каждый PNS.
1. Настройка концентратора уведомлений для взаимодействия с каждой PNS.

Следующие разделы содержат дополнительные инструкции по установке для каждой платформы.

### <a name="ios"></a>iOS

Следующие дополнительные действия должны выполняться для использования Apple Push Notification Service (APNS) из центра уведомлений Azure:

1. Создание запроса для сертификата push certificate с помощью средства доступа к цепочкам ключей подписи сертификата. Дополнительные сведения см. в разделе [создать файл запроса подписи сертификата для сертификата push certificate](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#generate-the-certificate-signing-request-file-for-the-push-certificate) в центре документации Azure.
1. Регистрация приложения Xamarin.Forms для поддержка Push-уведомлений в центре разработчиков Apple. Дополнительные сведения см. в разделе [Регистрация приложения для Push-уведомлений](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-app-for-push-notifications) в центре документации Azure.
1. Создание принудительной уведомлений включен профиль подготовки для приложения Xamarin.Forms в центре разработчиков Apple. Дополнительные сведения см. в разделе [создать профиль подготовки для приложения](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#create-a-provisioning-profile-for-the-app) в центре документации Azure.
1. Настройка концентратора уведомлений для связи с APNS. Дополнительные сведения см. в разделе [настройки концентратора уведомлений для APNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-apns).
1. Настройка приложения Xamarin.Forms для использования нового идентификатора приложения и профиль подготовки. Дополнительные сведения см. в разделе [Настройка проекта iOS в Xamarin Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-xamarin-studio) или [Настройка проекта iOS в Visual Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-visual-studio) в центре документации Azure.

### <a name="android"></a>Android

Для использования Firebase Cloud Messaging (FCM) из центра уведомлений Azure должны выполняться следующие дополнительные шаги:

1. Регистрация для FCM. Ключ API сервера и идентификатор клиента, будут автоматически созданы и упаковываются в `google-services.json` файла. Дополнительные сведения см. в разделе [включить Firebase Cloud Messaging (FCM)](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#enable-firebase-cloud-messaging-fcm).
1. Настройка концентратора уведомлений для взаимодействия с FCM. Дополнительные сведения см. в разделе [Настройка завершить обратно на мобильные приложения для отправки Push-запросов с использованием FCM](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm).

### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

Следующие дополнительные действия должны выполняться для использования службы уведомлений Windows (WNS) из центра уведомлений Azure:

1. Зарегистрируйтесь в службу уведомлений Windows (WNS). Дополнительные сведения см. в разделе [Регистрация приложения Windows для Push-уведомлений с помощью WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-windows-app-for-push-notifications-with-wns) в центре документации Azure.
1. Настройка концентратора уведомлений для связи с WNS. Дополнительные сведения см. в разделе [настройки концентратора уведомлений для WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-wns) в центре документации Azure.

## <a name="adding-push-notification-support-to-the-xamarinforms-application"></a>Добавляется поддержка Push-уведомлений в приложение Xamarin.Forms

В следующих разделах рассматриваются реализации, необходимые в каждом проекте под конкретную платформу для поддержки Push-уведомлений.

### <a name="ios"></a>iOS

Процесс реализации поддержка Push-уведомлений в приложения iOS выглядит следующим образом:

1. Регистрация с помощью Apple Push Notification Service (APNS) в `AppDelegate.FinishedLaunching` метод. Дополнительные сведения см. в разделе [регистрации в системе уведомлений Apple Push](#ios_register).
1. Реализуйте `AppDelegate.RegisteredForRemoteNotifications` метод для обработки ответа регистрации. Дополнительные сведения см. в разделе [обработки ответа регистрации](#ios_registration_response).
1. Реализуйте `AppDelegate.DidReceiveRemoteNotification` метод для обработки входящего Push-уведомлений. Дополнительные сведения см. в разделе [обработки входящего Push-уведомления](#ios_process_incoming).

<a name="ios_register" />

### <a name="registering-with-the-apple-push-notification-service"></a>Регистрация с помощью службы Apple Push-уведомлений

Прежде чем приложение iOS сможет получать Push-уведомлений, его необходимо зарегистрировать с помощью Apple Push Notification Service (APNS), который создаст уникальный маркер и вернуть его в приложение. В вызове регистрации `FinishedLaunching` в Переопределите `AppDelegate` класса:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    ...
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert, new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ...
}
```

При регистрации приложения iOS с помощью APNS его необходимо указать типы Push-уведомлений, которые хотите получать. `RegisterUserNotificationSettings` Метод регистрирует типы уведомлений, приложение может получать, с помощью `RegisterForRemoteNotifications` метод регистрации для получения Push-уведомлений из APNS.

> [!NOTE]
> Если не вызывается `RegisterUserNotificationSettings` метода приведет к Push-уведомлений, полученных без вмешательства пользователя приложения.

<a name="ios_registration_response" />

### <a name="handling-the-registration-response"></a>Обработка ответа регистрации

Запрос на регистрацию APNS происходит в фоновом режиме. При получении ответа операций ввода-вывода будет вызывать `RegisteredForRemoteNotifications` в Переопределите `AppDelegate` класса:

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyAPNS}
    };

    // Register for push with the Azure mobile app
    Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
    push.RegisterAsync(deviceToken, templates);
}
```

Этот метод создает простой шаблон уведомления как JSON и регистрирует устройство для получения шаблонных уведомлений из центра уведомлений.

> [!NOTE]
> `FailedToRegisterForRemoteNotifications` Переопределения должен быть реализован для обработки ситуаций, таких как отсутствует сетевое подключение. Это важно, так как пользователи могут запускать приложение при автономном режиме.

<a name="ios_process_incoming" />

### <a name="processing-incoming-push-notifications"></a>Обработки входящего Push-уведомлений

`DidReceiveRemoteNotification` В Переопределите `AppDelegate` класс используется для обработки входящего Push-уведомления, когда приложение работает и вызывается при получении уведомления:

```csharp
public override void DidReceiveRemoteNotification(
    UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
    NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

    string alert = string.Empty;
    if (aps.ContainsKey(new NSString("alert")))
        alert = (aps[new NSString("alert")] as NSString).ToString();

    // Show alert
    if (!string.IsNullOrEmpty(alert))
    {
      var notificationAlert = UIAlertController.Create("Notification", alert, UIAlertControllerStyle.Alert);
      notificationAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Cancel, null));
      UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(notificationAlert, true, null);
    }
}
```

`userInfo` Словарь содержит `aps` ключ, значение которого равно `alert` словаря с помощью оставшихся данных уведомлений. Этот словарь извлекается, с помощью `string` сообщение уведомления, отображаемых в диалоговом окне.

> [!NOTE]
> Если приложение не работает при получении Push-уведомления, приложение запустится, но `DidReceiveRemoteNotification` метод не сможет обрабатывать уведомления. Вместо этого получить полезные данные уведомления и реагировать соответствующим образом из `WillFinishLaunching` или `FinishedLaunching` переопределяет.

Дополнительные сведения о APNS, см. в разделе [Push-уведомлений в iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md).

### <a name="android"></a>Android

Процесс реализации поддержка Push-уведомлений в приложение на платформе Android выглядит следующим образом:

1. Добавить [Xamarin.Firebase.Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet пакета проекта Android и задать целевой версии приложения для Android 7.0 или более поздней версии.
1. Добавить `google-services.json` файл, Скачанный из консоли Firebase, в корень проекта Android и задайте его действие построения для **GoogleServicesJson**. Дополнительные сведения см. в разделе [добавьте JSON-файла служб Google](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).
1. Регистрация с помощью Firebase Cloud Messaging (FCM) путем объявления получателя в манифест Android файла и по реализации `FirebaseRegistrationService.OnTokenRefresh` метод. Дополнительные сведения см. в разделе [регистрация в Firebase Cloud Messaging](#android_register_fcm).
1. Регистрация в концентраторе уведомлений Azure в `AzureNotificationHubService.RegisterAsync` метод. Дополнительные сведения см. в разделе [регистрацию в центре уведомлений Azure](#android_register_azure).
1. Реализуйте `FirebaseNotificationService.OnMessageReceived` метод для обработки входящего Push-уведомлений. Дополнительные сведения см. в разделе [отображение содержимого Push-уведомление](#android_displaying_notification).

Дополнительные сведения о Firebase Cloud Messaging, см. в разделе [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) и [удаленные уведомления с помощью Firebase Cloud Messaging](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

<a name="android_register_fcm" />

#### <a name="registering-with-firebase-cloud-messaging"></a>Регистрация в Firebase Cloud Messaging

Прежде чем приложение Android может получать Push-уведомлений, его необходимо зарегистрировать в FCM, который создаст маркер регистрации и вернуть его в приложение. Дополнительные сведения о маркерах регистрации, см. в разделе [регистрации в FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#registration).

Это достигается путем:

- Объявление получателя в манифесте Android. Дополнительные сведения см. в разделе [объявления получателя в манифесте Android](#declaring_a_receiver).
- Реализация службы идентификатора экземпляра Firebase. Дополнительные сведения см. в разделе [реализация службы идентификатора экземпляра Firebase](#implementing-firebase-instance-id-service).

<a name="declaring_a_receiver" />

##### <a name="declaring-the-receiver-in-the-android-manifest"></a>Объявление получателя в манифест Android

Изменить **AndroidManifest.xml** и вставьте следующий `<receiver>` элементы в `<application>` элемент:

```xml
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
  <intent-filter>
    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
    <category android:name="${applicationId}" />
  </intent-filter>
</receiver>
```

Этот XML-код выполняет следующие операции:

- Объявляет внутреннего `FirebaseInstanceIdInternalReceiver` реализацию, используемую для безопасного запуска служб.
- Объявляет `FirebaseInstanceIdReceiver` реализация, которая предоставляет уникальный идентификатор для каждого экземпляра приложения. Также данный получатель проверяет подлинность и авторизует действия.

`FirebaseInstanceIdReceiver` Получает `FirebaseInstanceId` и `FirebaseMessaging` события и доставляет их в класс, производный от `FirebasesInstanceIdService`.

<a name="implementing-firebase-instance-id-service" />

##### <a name="implementing-the-firebase-instance-id-service"></a>Реализация службы идентификатора экземпляра Firebase

Регистрация приложения для использования FCM достигается путем наследования от класса `FirebaseInstanceIdService` класса. Этот класс отвечает за создание маркеров безопасности, который разрешает клиентскому приложению доступ к FCM. В примере приложения `FirebaseRegistrationService` класс является производным от `FirebaseInstanceIdService` класса и показан в следующем примере кода:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    const string TAG = "FirebaseRegistrationService";

    public override void OnTokenRefresh()
    {
        var refreshedToken = FirebaseInstanceId.Instance.Token;
        Log.Debug(TAG, "Refreshed token: " + refreshedToken);
        SendRegistrationTokenToAzureNotificationHub(refreshedToken);
    }

    void SendRegistrationTokenToAzureNotificationHub(string token)
    {
        // Update notification hub registration
        Task.Run(async () =>
        {
            await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
        });
    }
}
```

`OnTokenRefresh` Метод вызывается, когда приложение получает маркер регистрации из FCM. Этот метод извлекает маркер из `FirebaseInstanceId.Instance.Token` свойство, которое асинхронно обновляется средствами FCM. `OnTokenRefresh` Метод вызывается редко, так как маркер обновляется только в том случае, когда приложение установки или удаления, когда пользователь удаляет данные приложения при его удалении идентификатора экземпляра, после или маркера безопасности под угрозой. Кроме того служба идентификатора экземпляра FCM потребует, что приложение периодически обновляется в свой маркер, обычно каждые 6 месяцев.

`OnTokenRefresh` Также вызывает метод `SendRegistrationTokenToAzureNotificationHub` метод, который позволяет связать маркер регистрации пользователя в центре уведомлений Azure.

<a name="android_register_azure" />

#### <a name="registering-with-the-azure-notification-hub"></a>Регистрация в концентраторе уведомлений Azure

`AzureNotificationHubService` Класс предоставляет `RegisterAsync` метод, который связывает пользователя маркер регистрации в центре уведомлений Azure. В следующем коде показано в примере `RegisterAsync` метод, который вызывается `FirebaseRegistrationService` класса при изменении маркер регистрации пользователя:

```csharp
public class AzureNotificationHubService
{
    const string TAG = "AzureNotificationHubService";

    public static async Task RegisterAsync(Push push, string token)
    {
        try
        {
            const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBody}
            };

            await push.RegisterAsync(token, templates);
            Log.Info("Push Installation Id: ", push.InstallationId.ToString());
        }
        catch (Exception ex)
        {
            Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
        }
    }
}
```

Этот метод создает простой шаблон уведомления как JSON и регистрируется для получения шаблонных уведомлений из центра уведомлений, с помощью маркера регистрации Firebase. Это гарантирует, что все уведомления, отправленные из концентратора уведомлений Azure будут направляться на устройство, представленного маркером регистрации.

<a name="android_displaying_notification" />

#### <a name="displaying-the-contents-of-a-push-notification"></a>Отображение содержимого Push-уведомление

Отображение содержимого Push-уведомление достигается путем наследования от класса `FirebaseMessagingService` класса. Этот класс включает переопределяемый `OnMessageReceived` метод, который вызывается, когда приложение получает уведомление от FCM, указано, что приложение выполняется на переднем плане. В примере приложения `FirebaseNotificationService` класс является производным от `FirebaseMessagingService` класса и показано в следующем примере кода:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseNotificationService : FirebaseMessagingService
{
    const string TAG = "FirebaseNotificationService";

    public override void OnMessageReceived(RemoteMessage message)
    {
        Log.Debug(TAG, "From: " + message.From);

        // Pull message body out of the template
        var messageBody = message.Data["message"];
        if (string.IsNullOrWhiteSpace(messageBody))
            return;

        Log.Debug(TAG, "Notification message body: " + messageBody);
        SendNotification(messageBody);
    }

    void SendNotification(string messageBody)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
            .SetContentTitle("New Todo Item")
            .SetContentText(messageBody)
            .SetContentIntent(pendingIntent)
            .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
            .SetAutoCancel(true);

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }
}
```

Когда приложение получает уведомление от FCM, `OnMessageReceived` метод извлекает содержимое сообщения, а также вызывает `SendNotification` метод. Этот метод преобразует содержимое сообщения в локальное уведомление, которое запускается во время работы приложения, с помощью уведомление отображается в области уведомлений.

##### <a name="handling-notification-intents"></a>Обработка уведомлений намерений

Когда пользователь касается уведомления, любые данные, сопровождающие сообщения уведомления стала доступной на `Intent` дополнения. Эти данные могут быть извлечены следующим кодом:

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

Запуск приложений `Intent` возникает, когда пользователь касается его сообщение уведомления, поэтому этот код добавляет в журнал содержащиеся в ней данные в `Intent` в окне вывода.

### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

Прежде чем универсальной платформы Windows (UWP), приложение может получать Push-уведомлений, его необходимо зарегистрировать с помощью Windows Notification Service (WNS), который возвратит канала уведомления. Вызывается регистрации `InitNotificationsAsync` метод в `App` класса:

```csharp
private async Task InitNotificationsAsync()
{
    var channel = await PushNotificationChannelManager
          .CreatePushNotificationChannelForApplicationAsync();

    const string templateBodyWNS =
        "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

    JObject headers = new JObject();
    headers["X-WNS-Type"] = "wns/toast";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyWNS},
        {"headers", headers} // Needed for WNS.
    };

    await TodoItemManager.DefaultManager.CurrentClient.GetPush()
          .RegisterAsync(channel.Uri, templates);
}
```

Этот метод получает канал Push-уведомлений, создает шаблон сообщений уведомления как JSON и регистрирует устройство для получения шаблонных уведомлений из центра уведомлений.

`InitNotificationsAsync` Метод вызывается из `OnLaunched` в Переопределите `App` класса:

```csharp
protected override async void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    await InitNotificationsAsync();
}
```

Это гарантирует, что регистрации Push-уведомлений создаваться или обновляться при каждом запуске приложения, таким образом гарантируя, что Push-канал WNS всегда активен.

При получении Push-уведомление будет автоматически отображаться как *всплывающих* — немодальное окно, содержащее сообщение.

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать центры уведомлений Azure для отправки Push-уведомлений из мобильных приложений Azure экземпляра приложения Xamarin.Forms. Концентраторы уведомлений Azure предоставляют масштабируемые Push-инфраструктуру для отправки мобильные Push-уведомления из любой серверной части на любую мобильную платформу, устраняя сложности серверной части, необходимость связываться с разных систем уведомлений платформы.


## <a name="related-links"></a>Связанные ссылки

- [Использование мобильного приложения Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Центры уведомлений Azure](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)
- [Добавление Push-уведомлений в приложение Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/)
- [Push-уведомлений в iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)
- [Обмен сообщениями Firebase Cloud](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [TodoAzurePush (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)
- [Azure SDK для мобильного клиента](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
