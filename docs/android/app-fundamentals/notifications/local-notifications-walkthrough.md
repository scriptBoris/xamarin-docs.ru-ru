---
title: Пошаговое руководство. Использование локальной уведомлений в Xamarin.Android
description: В этом пошаговом руководстве демонстрируется использование локального уведомления Xamarin.Android приложений. Он демонстрирует основные принципы создания и публикации локальное уведомление. Когда пользователь щелкает уведомления в области уведомлений, запуске второго действия.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/30/2018
ms.openlocfilehash: a2ca3755e3201263584447ba47ec36d2096386da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766586"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Пошаговое руководство. Использование локальной уведомлений в Xamarin.Android

_В этом пошаговом руководстве демонстрируется использование локального уведомления Xamarin.Android приложений. Он демонстрирует основные принципы создания и публикации локальное уведомление. Когда пользователь щелкает уведомления в области уведомлений, запуске второго действия._


## <a name="overview"></a>Обзор

В этом пошаговом руководстве мы создадим приложение Android, которое вызывает уведомление, когда пользователь нажимает кнопку в действии. Когда пользователь щелкает уведомление, он запускает второго действия, которое отображает количество бы он нажал кнопку в первое действие.

Следующих снимках экрана показаны некоторые примеры этого приложения.

[![Снимки экрана примера с уведомлением](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)



## <a name="walkthrough"></a>Пошаговое руководство

Чтобы начать, давайте создадим новый проект Android с помощью **приложения Android** шаблона. Давайте назовем этот проект **LocalNotifications**. (Если вы не знакомы с созданием проектам Xamarin.Android, см. раздел [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md).)


### <a name="add-the-androidsupportv4app-component"></a>Добавьте компонент Android.Support.V4.App

В этом пошаговом руководстве мы используем `NotificationCompat.Builder` для построения нашей локального уведомления. Как описано в статье [локального уведомления](~/android/app-fundamentals/notifications/local-notifications.md), необходимо включить [библиотеку поддержки Android версии 4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet в проект для использования `NotificationCompat.Builder`.

Теперь изменим **MainActivity.cs** и добавьте следующий `using` инструкции, чтобы типы в `Android.Support.V4.App` доступны для нашего кода:

```csharp
using Android.Support.V4.App;
```

Кроме того, мы его необходимо сделать для компилятора, мы используем `Android.Support.V4.App` версии `TaskStackBuilder` вместо `Android.App` версии. Добавьте следующие `using` инструкции для разрешения неоднозначности:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```


### <a name="define-the-notification-id"></a>Определите идентификатор уведомления

Нам потребуется уникальный идентификатор для наших уведомления. Изменим **MainActivity.cs** и добавьте следующую переменную статического экземпляра для `MainActivity` класса:

```csharp
private static readonly int ButtonClickNotificationId = 1000;
```


### <a name="add-code-to-generate-the-notification"></a>Добавьте код для создания уведомления

Затем нужно создать новый обработчик событий для кнопки `Click` событий. Добавьте следующий метод `MainActivity`:

```csharp
private void ButtonOnClick (object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    Bundle valuesForActivity = new Bundle();
    valuesForActivity.PutInt ("count", count);

    // When the user clicks the notification, SecondActivity will start up.
    Intent resultIntent = new Intent(this, typeof (SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras (valuesForActivity);

    // Construct a back stack for cross-task navigation:
    TaskStackBuilder stackBuilder = TaskStackBuilder.Create (this);
    stackBuilder.AddParentStack (Java.Lang.Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent (resultIntent);

    // Create the PendingIntent with the back stack:            
    PendingIntent resultPendingIntent =
        stackBuilder.GetPendingIntent (0, (int)PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
        .SetAutoCancel (true)                    // Dismiss from the notif. area when clicked
        .SetContentIntent (resultPendingIntent)  // Start 2nd activity when the intent is clicked.
        .SetContentTitle ("Button Clicked")      // Set its title
        .SetNumber (count)                       // Display the count in the Content Info
        .SetSmallIcon(Resource.Drawable.ic_stat_button_click)  // Display this icon
        .SetContentText (String.Format(
            "The button has been clicked {0} times.", count)); // The message to display.

    // Finally, publish the notification:
    NotificationManager notificationManager =
        (NotificationManager)GetSystemService(Context.NotificationService);
    notificationManager.Notify(ButtonClickNotificationId, builder.Build());

    // Increment the button press count:
    count++;
}
```

В `OnCreate` метод, назначьте его `ButtonOnClick` метод `Click` событие кнопки (Замените обработчик событий делегата, предоставляемого шаблоном):

```csharp
button.Click += ButtonOnClick;
```


### <a name="create-a-second-activity"></a>Создание второго действия

Теперь необходимо создать другое действие, Android будет отображаться, когда пользователь щелкает наши уведомления. Добавьте еще одно действие Android проект называется **SecondActivity**. Откройте **SecondActivity.cs** и замените его содержимое следующим кодом:

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
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Get the count value passed to us from MainActivity:
            int count = Intent.Extras.GetInt ("count", -1);

            // No count was passed? Then just return.
            if (count <= 0)
                return;

            // Display the count sent from the first activity:
            SetContentView (Resource.Layout.Second);
            TextView textView = FindViewById<TextView>(Resource.Id.textView);
            textView.Text = String.Format (
                "You clicked the button {0} times in the previous activity.", count);
        }
    }
}
```

Также необходимо создать макет ресурсов для **SecondActivity**. Добавьте новый **Android макета** проект называется файл **Second.axml**. Изменить **Second.axml** и вставьте следующий код макета:

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
        android:id="@+id/textView" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>Добавить значок уведомления

Наконец добавим мелкого значка, который будет отображаться в области уведомлений при запуске наши уведомления. Можно скопировать [этот значок](local-notifications-walkthrough-images/ic-stat-button-click.png) в проект или создать собственный пользовательский значок. Мы назовем файл значка **ic\_stat\_кнопку\_click.png** и скопируйте его в **ресурсы или drawable** папки. Не забывайте использовать **Добавить > существующий элемент...**  для включения этого значка файла в проект.


### <a name="run-the-application"></a>Запуск приложения

Давайте Постройте и запустите приложение. Вы увидите первое действие похоже на следующий снимок экрана:

[![Снимок экрана первого действия](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

При нажатии кнопки, можно заметить мелкий значок для уведомления появляются в области уведомлений:

[![Появится значок уведомления](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Если проведите вниз и предоставлять уведомления лоток, вы увидите уведомление:

[![Сообщение уведомления](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Когда щелкните уведомление, она должна исчезнуть и наших других действий, должны запускаться &ndash; ищете нечто похожее на следующем снимке экрана:

[![Снимок экрана второго действия](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

Поздравляем! На этом этапе выполнения пошагового руководства Android локального уведомления и у вас есть рабочий образец, который можно ссылаться на. Намного больше на уведомления не было показано здесь, поэтому нужна дополнительная информация, взгляните на [документации Google уведомления о](http://developer.android.com/guide/topics/ui/notifiers/notifications.html) и Android [уведомления](http://developer.android.com/design/patterns/notifications.html) руководство по проектированию.



## <a name="summary"></a>Сводка

В этом пошаговом руководстве мы использовали `NotificationCompat.Builder` для создания и отображения уведомлений. Мы видели основной пример запуска второго действия, чтобы отвечать на действия пользователя с уведомлением, и мы показали передачу данных от первого действия второго действия.


## <a name="related-links"></a>Связанные ссылки

- [LocalNotifications (пример)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Шаблоны проектирования руководства на уведомления](http://developer.android.com/design/patterns/notifications.html)
- [уведомления](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
