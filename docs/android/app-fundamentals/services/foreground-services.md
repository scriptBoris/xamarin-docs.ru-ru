---
title: Службы переднего плана
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/19/2018
ms.openlocfilehash: 3088fa4b5cfa21ac57533ef331ffcc15414e14b4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763752"
---
# <a name="foreground-services"></a>Службы переднего плана

Служба переднего плана — это специальный тип привязанных служба или служба, запускаемая. Иногда службы будет выполнять задачи, которые пользователи должны учитывать активно, эти службы, называются _служб переднего плана_. Примером службы переднего плана — это приложение, предоставляющего пользователя с направлениями при пешком или на машине. Даже если приложение находится в фоновом режиме, важно наличие достаточных ресурсах для правильной работы службы и что у пользователя есть быстрый и удобный способ доступа к приложению. Для приложения Android, это означает, что служба переднего плана должна получить высокий приоритет, чем «стандартными» службы и службы переднего плана необходимо указать `Notification` отображения Android при условии, что служба запущена.
 
Для запуска службы переднего плана, приложение выполняет передачу цель, который сообщает Android для запуска службы. Затем служба должен зарегистрироваться как служба переднего плана с Android. Приложения, выполняющиеся на Android 8.0 (или более поздней версии) следует использовать `Context.StartForegroundService` метод, чтобы запустить службу, хотя должна использоваться для приложений, работающих на устройствах с помощью более старой версии Android `Context.StartService`

Этот метод расширения C# является примером по запуску службы переднего плана. В Android 8.0 и более поздних версий будет использовать `StartForegroundService` метода, в противном случае более старой версии `StartService` будет использоваться метод.  

```csharp
public static void StartForegroundServiceComapt<T>(this Context context, Bundle args = null) where T : Service
{
    var intent = new Intent(context, typeof(T));
    if (args != null) 
    {
        intent.PutExtras(args);
    }

    if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.O)
    {
        context.StartForegroundService(intent);
    }
    else
    {
        context.StartService(intent);
    }
}
```

## <a name="registering-as-a-foreground-service"></a>Регистрация в качестве службы переднего плана

После запуска службы переднего плана, должен зарегистрироваться с Android с помощью вызова [ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/). Если служба запущена с `Service.StartForegroundService` метода, но не регистрирует себя, то Android остановите службу, а флаг приложения не отвечают.

`StartForeground` принимает два параметра, оба из которых являются обязательными.
 
* Целочисленное значение, уникальное в пределах приложения для идентификации службы.
* Объект `Notification` объект, который Android будут отображаться в строке состояния, при условии, что служба запущена.

Android будут отображаться в строке состояния для уведомления, при условии, что служба запущена. Уведомления, как минимум, предоставит визуальная подсказка для пользователя, на котором выполняется служба. В идеальном случае уведомления должен предусматривать ярлык для приложения или возможно, некоторые кнопки действия для управления приложения. Примером этого является музыкальный проигрыватель &ndash; уведомление, которое отображается, возможно, приостановка и воспроизведение музыки перемотку назад предыдущая композиция или перейдите к следующей песни кнопок. 

Этот фрагмент кода приведен пример регистрации службы в качестве службы переднего плана:   

```csharp
// This is any integer value unique to the application.
public const int SERVICE_RUNNING_NOTIFICATION_ID = 10000;

public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
{
    // Code not directly related to publishing the notification has been omitted for clarity.
    // Normally, this method would hold the code to be run when the service is started.
    
    var notification = new Notification.Builder(this)
        .SetContentTitle(Resources.GetString(Resource.String.app_name))
        .SetContentText(Resources.GetString(Resource.String.notification_text))
        .SetSmallIcon(Resource.Drawable.ic_stat_name)
        .SetContentIntent(BuildIntentToShowMainActivity())
        .SetOngoing(true)
        .AddAction(BuildRestartTimerAction())
        .AddAction(BuildStopServiceAction())
        .Build();

    // Enlist this instance of the service as a foreground service
    StartForeground(SERVICE_RUNNING_NOTIFICATION_ID, notification);
}
```

Предыдущего уведомления отобразится уведомление о состоянии панели, который аналогичен следующему:

![Изображение уведомления в строке состояния](foreground-services-images/foreground-services-01.png "изображение уведомления в строке состояния")

В области уведомлений с двумя действиями, позволяющих пользователю контролировать службы в этом снимке экрана показан расширенный уведомления:

![Изображение развернутой уведомления](foreground-services-images/foreground-services-02.png "изображение развернутой уведомления.")

Дополнительные сведения об уведомлениях о доступна в [локального уведомления](~/android/app-fundamentals/notifications/local-notifications.md) раздел [уведомлений Android](~/android/app-fundamentals/notifications/index.md) руководства.

## <a name="unregistering-as-a-foreground-service"></a>Отмена регистрации в качестве службы переднего плана

Службы можно отменять перечисляется как основной службы путем вызова метода `StopForeground`. `StopForeground` не останавливает службу, но он приведет к удалению значок уведомления и сигналы Android, эта служба может завершить работу при необходимости.

Также можно удалить панель уведомления о состоянии, которое отображается, передав `true` методу: 

```csharp
StopForeground(true);
```

Если служба останавливается на вызов `StopSelf` или `StopService`, уведомление панели состояния будут удалены.

## <a name="related-links"></a>Связанные ссылки

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForegrond](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [Локальные уведомления](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
