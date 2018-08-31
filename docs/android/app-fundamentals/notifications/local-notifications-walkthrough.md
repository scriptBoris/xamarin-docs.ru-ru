---
title: Пошаговое руководство. Использование локальных уведомлений в Xamarin.Android
description: В этом пошаговом руководстве демонстрируется использование локальных уведомлений в приложениях Xamarin.Android. Он демонстрирует основные принципы создания и публикации локального уведомления. Когда пользователь щелкает уведомления в области уведомлений, запуске второго действия.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/16/2018
ms.openlocfilehash: a2ca3755e3201263584447ba47ec36d2096386da
ms.sourcegitcommit: f9fb316344fc4dce2fdce0dde3c5f4c2e4a42d08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "30766586"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Пошаговое руководство. Использование локальных уведомлений в Xamarin.Android

_В этом пошаговом руководстве демонстрируется использование локальных уведомлений в приложениях Xamarin.Android. Он демонстрирует основные принципы создания и публикации локального уведомления. Когда пользователь щелкает уведомления в области уведомлений, запуске второго действия._


## <a name="overview"></a>Обзор

В этом пошаговом руководстве мы создадим приложение Android, которое вызывает уведомление, когда пользователь нажимает кнопку в действии. Когда пользователь щелкает уведомления, она запускает второе действие, которое отображает, сколько раз щелчка кнопки в первом действии.

Далее на снимках экрана показаны некоторые примеры этого приложения.

[![Снимки экрана с уведомлением](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> Это руководство посвящено [API-интерфейсы NotificationCompat](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) из [библиотека поддержки Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Эти API-интерфейсы будут обеспечения максимально обратной совместимости для Android 4.0 (уровень API 14).

## <a name="creating-the-project"></a>Создание проекта

Чтобы начать, давайте создадим новый проект Android с помощью **приложение Android** шаблона. Давайте назовем этот проект **LocalNotifications**. (Если вы не знакомы с созданием проектов Xamarin.Android, см. в разделе [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md).)

Измените файл ресурсов **values/Strings.xml** , чтобы он содержал два дополнительных строковых ресурсов, которые будут использоваться, когда наступает время для создания канала уведомления:


```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>Добавьте пакет Android.Support.V4 NuGet

В этом пошаговом руководстве мы используем `NotificationCompat.Builder` для создания нашей локальное уведомление. Как описано в [локальных уведомлений](~/android/app-fundamentals/notifications/local-notifications.md), необходимо включить [библиотеки поддержки Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet в нашем проекте для использования `NotificationCompat.Builder`.

Затем изменим **MainActivity.cs** и добавьте следующие `using` инструкции, чтобы типы в `Android.Support.V4.App` доступны для нашего кода:

```csharp
using Android.Support.V4.App;
```

Кроме того, мы должны сделать снимите флажок, чтобы компилятору, что мы используем `Android.Support.V4.App` версии `TaskStackBuilder` вместо `Android.App` версии. Добавьте следующий `using` инструкцию, чтобы устранить неоднозначность:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>Создание канала уведомлений

Добавьте метод `MainActivity` , создаст канал уведомления, (при необходимости):

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

    var name = Resources.GetString(Resource.String.channel_name);
    var description = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, name, NotificationImportance.Default)
                  {
                      Description = description
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Обновление `OnCreate` метод для вызова этого нового метода:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```


### <a name="define-the-notification-id"></a>Определите идентификатор уведомления

Нам понадобится уникальный идентификатор для нашего уведомления и уведомления. Изменим то **MainActivity.cs** и добавьте следующую переменную статический экземпляр для `MainActivity` класса:

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>Добавьте код для создания уведомления

Далее нам необходимо создать новый обработчик событий для кнопки `Click` событий. Добавьте следующий метод в `MainActivity`:

```csharp
void ButtonOnClick(object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    var valuesForActivity = new Bundle();
    valuesForActivity.PutInt(COUNT_KEY, count);

    // When the user clicks the notification, SecondActivity will start up.
    var resultIntent = new Intent(this, typeof(SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras(valuesForActivity);

    // Construct a back stack for cross-task navigation:
    var stackBuilder = TaskStackBuilder.Create(this);
    stackBuilder.AddParentStack(Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent(resultIntent);

    // Create the PendingIntent with the back stack:
    var resultPendingIntent = stackBuilder.GetPendingIntent(0, (int) PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    var builder = new NotificationCompat.Builder(this, CHANNEL_ID)
                  .SetAutoCancel(true) // Dismiss the notification from the notification area when the user clicks on it
                  .SetContentIntent(resultPendingIntent) // Start up this activity when the user clicks the intent.
                  .SetContentTitle("Button Clicked") // Set the title
                  .SetNumber(count) // Display the count in the Content Info
                  .SetSmallIcon(Resource.Drawable.ic_stat_button_click) // This is the icon to display
                  .SetContentText($"The button has been clicked {count} times."); // the message to display.

    // Finally, publish the notification:
    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(NOTIFICATION_ID, builder.Build());

    // Increment the button press count:
    count++;
}
```

`OnCreate` Метод MainActivity необходимо сделать вызов для создания канала уведомления и назначения `ButtonOnClick` метод `Click` события кнопки (Замените обработчик событий делегата, предоставленный шаблоном):

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();

    // Display the "Hello World, Click Me!" button and register its event handler:
    var button = FindViewById<Button>(Resource.Id.MyButton);
    button.Click += ButtonOnClick;
}
```


### <a name="create-a-second-activity"></a>Создайте вторую операцию

Теперь необходимо создать другое действие, Android будет отображаться, когда пользователь щелкает наших уведомлений. Добавьте еще одно действие Android в проект с именем **SecondActivity**. Откройте **SecondActivity.cs** и замените его содержимое следующим кодом:

```csharp
using System;
using Android.App;
using Android.OS;
using Android.Widget;

namespace LocalNotifications
{
    [Activity(Label = "Second Activity")]
    public class SecondActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            // Get the count value passed to us from MainActivity:
            var count = Intent.Extras.GetInt(MainActivity.COUNT_KEY, -1);

            // No count was passed? Then just return.
            if (count <= 0)
            {
                return;
            }

            // Display the count sent from the first activity:
            SetContentView(Resource.Layout.Second);
            var txtView = FindViewById<TextView>(Resource.Id.textView1);
            txtView.Text = $"You clicked the button {count} times in the previous activity.";
        }
    }
}
```

Мы также необходимо создать макет ресурсов для **SecondActivity**. Добавьте новый **макет Android** файл в проект с именем **Second.axml**. Изменить **Second.axml** и вставьте в него следующий код макета:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text=""
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/textView1" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>Добавление значка уведомления

Наконец добавьте мелкого значка, который будет отображаться в области уведомлений при запуске уведомление. Вы можете скопировать [этот значок](local-notifications-walkthrough-images/ic-stat-button-click.png) в проект или создайте собственный пользовательский значок. Имя файла значка **ic\_stat\_кнопку\_click.png** и скопируйте его **ресурсы/drawable** папки. Не забывайте использовать **Добавить > существующий элемент...**  разместит этот файл значка в проекте.


### <a name="run-the-application"></a>Запуск приложения

Выполните сборку и запуск приложения. Вы увидите первое действие, как на следующем снимке экрана:

[![Первый снимок экрана действия](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

При нажатии кнопки, можно заметить, что небольшой значок уведомления отображается в области уведомлений:

[![Появится значок уведомления](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Если проведите пальцем вниз и предоставлять панель уведомлений, вы увидите уведомление:

[![Сообщение уведомления](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Когда щелкните уведомление, оно должно исчезнуть и наших других действий должен запускаться &ndash; поиска немного как на следующем снимке экрана:

[![Второй снимок экрана действия](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

Поздравляем! На этом этапе вы выполнили Пошаговое руководство по Android локальное уведомление, и у вас есть рабочий образец, который можно ссылаться на. Сведения на уведомления о чем мы показали здесь, поэтому если вам нужна дополнительная информация взгляните на множество [документации Google о уведомления](http://developer.android.com/guide/topics/ui/notifiers/notifications.html).


## <a name="summary"></a>Сводка

В этом пошаговом руководстве используется `NotificationCompat.Builder` для создания и отображения уведомлений. Он показал простой пример того, как запуск второго действия, чтобы отвечать на действия пользователя с уведомлением и демонстрируется передача данных из первого действия для второго действия.


## <a name="related-links"></a>Связанные ссылки

- [LocalNotifications (пример)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Каналы уведомления Android Oreo](https://blog.xamarin.com/android-oreo-notification-channels/)
- [Уведомление](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
