---
title: Службы переднего плана
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/19/2018
ms.openlocfilehash: 47e1eda2f701b654f81f664050847677fba8bcc5
ms.sourcegitcommit: be4da0cd7e1a915e3b8932a7e3d6bcd74c7055be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38986038"
---
# <a name="foreground-services"></a>Службы переднего плана

Службы переднего плана — это специальный тип привязанной службы или службы. Иногда службы будет выполнять задачи, которые пользователи должны учитывать активно, эти службы называются _службы переднего плана_. Пример службы переднего плана — это приложение, которое предоставляет пользователю с направлениями при пешком или на машине. Даже если приложение находится в фоновом режиме, это по-прежнему важно, что служба имеет достаточно ресурсов для правильной работы и что у пользователя есть быстрый и удобный способ доступа к приложению. Приложения для Android, это означает, что службы переднего плана должны получить высокий приоритет, чем «regular» службы и службы переднего плана необходимо указать `Notification` , Android будет отображаться до тех пор, пока служба запущена.
 
Для запуска службы переднего плана, приложение выполняет передачу объекта Intent, сообщающий Android для запуска службы. Затем службы необходимо зарегистрировать себя в качестве службы переднего плана с Android. В приложениях, работающих под управлением Android 8.0 (или более поздней версии) должны использоваться `Context.StartForegroundService` метод, чтобы запустить службу, хотя следует использовать приложения, работающие на устройствах с помощью более старой версии Android `Context.StartService`

Этот метод расширения C# является примером того, как для запуска службы переднего плана. В Android 8.0 и более поздних версий будет использоваться `StartForegroundService` метод, в противном случае более старой версии `StartService` будет использоваться метод.  

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

После запуска службы переднего плана должен зарегистрироваться с Android путем вызова [ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/). Если служба запущена с `Service.StartForegroundService` метод, но не регистрирует, то Android будет остановить службу и пометить приложение как не отвечающий на запросы.

`StartForeground` принимает два параметра, оба из которых являются обязательными.
 
* Целочисленное значение, которое является уникальным в пределах приложения для идентификации службы.
* Объект `Notification` который Android отображает в строке состояния для до тех пор, пока служба запущена.

Android будет уведомление отображается в строке состояния для до тех пор, пока служба запущена. Уведомления, как минимум, будет визуальная подсказка для пользователя, на котором выполняется служба. В идеальном случае уведомления должен предусматривать ярлык для приложения или возможно, некоторые кнопки действий для управления приложением. Примером этого является плейер &ndash; уведомление, которое отображается, возможно кнопок Пауза и воспроизведение музыки, Перемотка назад для разделения или переходите к следующей песни. 

В этом фрагменте кода приведен пример регистрации службы в качестве службы переднего плана:   

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

Предыдущего уведомления для отображения панели уведомление о состоянии, что это следующего вида:

![Изображение уведомления в строке состояния](foreground-services-images/foreground-services-01.png "изображение уведомления в строке состояния")

На этом снимке экрана показано развернутое уведомление в области уведомлений с двумя действиями, которые позволяют пользователю управлять этой службой:

![Изображение, показывающее расширенные уведомления](foreground-services-images/foreground-services-02.png "изображение расширенные уведомления.")

Дополнительные сведения об уведомлениях о [локальных уведомлений](~/android/app-fundamentals/notifications/local-notifications.md) раздел [уведомлений Android](~/android/app-fundamentals/notifications/index.md) руководства.

## <a name="unregistering-as-a-foreground-service"></a>Отмена регистрации в качестве службы переднего плана

Службы можно отменить перечисляется как службы переднего плана путем вызова метода `StopForeground`. `StopForeground` не останавливает службу, но он удалит значок уведомления и передает сигнал, Android, эта служба может завершить работу, при необходимости.

Также можно удалить панель уведомление о состоянии, которое отображается, передав `true` методу: 

```csharp
StopForeground(true);
```

Если служба останавливается с помощью вызова `StopSelf` или `StopService`, уведомление панели состояния будут удалены.

## <a name="related-links"></a>Связанные ссылки

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForeground](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [Локальные уведомления](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
