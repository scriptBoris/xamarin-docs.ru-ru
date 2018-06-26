---
title: Рассылка приемников в Xamarin.Android
description: В этом разделе описывается использование приемником широковещательных пакетов.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 04/20/2018
ms.openlocfilehash: 6b2e316eaf67e51801be4fcd670e80ec81c8ff08
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935403"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Рассылка приемников в Xamarin.Android

_В этом разделе описывается использование приемником широковещательных пакетов._

## <a name="broadcast-receiver-overview"></a>Общие сведения о широковещательных получателя

Объект _широковещательных получателя_ — это компонент Android, который позволяет приложению реагировать на сообщения (Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)), рассылаются Android операционной системой или приложением. Выполните широковещательных рассылок _публикации подписки_ модели &ndash; событие вызывает вещания должны публиковаться и полученных те компоненты, которые нужны в событии. 

Android определяет два типа пакетов:

* **Явные вещания** &ndash; эти типы широковещательного целевой конкретного приложения. Наиболее распространенные способы использования явное вещания — начать действие. Пример явной вещания, когда приложению нужен для набора номера телефона; он отправляет целью, предназначенного для телефона Android и проход вдоль номер телефона, чтобы осуществить. Затем Android направит намерение приложения телефона.
* **Неявные вещания** &ndash; эти широковещательные пакеты отправляются на все приложения на устройстве. Пример неявного вещания — `ACTION_POWER_CONNECTED` цель. Это назначение публикуется каждый раз, когда Android обнаруживает, что на устройстве зарядки. Android направит это назначение для всех приложений, которые зарегистрированы для данного события.

Широковещательный получатель является подклассом `BroadcastReceiver` типа и его необходимо переопределить [ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/) метод. Будет выполняться Android `OnReceive` в основном потоке, поэтому этот метод должны разрабатываться выполняться быстро. Следует соблюдать осторожность при создании процесса потоков в `OnReceive` так, как Android может завершить процесс при завершении метода. Если широковещательных приемника выполнить долго выполняемые действия, то рекомендуется запланировать _задания_ с помощью `JobScheduler` или _диспетчера заданий Firebase_. Планирование работы с заданием обсуждаются отдельные руководства.

_Намерения фильтр_ используется для регистрации приемником широковещательной рассылки, чтобы Android правильной передачи сообщений. Блокировка с намерением фильтра можно указать во время выполнения (это иногда называется _контекст зарегистрирован получателя_ или как _динамическую регистрацию_) или можно статически определить в Android ( манифеста_зарегистрированные манифеста получателя_). Xamarin.Android предоставляет атрибут C# `IntentFilterAttribute`, статически, зарегистрирует намерения фильтр (это обсуждаются более подробно далее в этом руководстве). Начиная с версии Android 8.0, это не приложение статически регистрации для неявного широковещательной рассылки.

Основное различие между получатель зарегистрированные манифеста и получателя контекст зарегистрирован — что получателя контекст зарегистрирован будет отвечать только на широковещательные рассылки во время выполнения приложения во время регистрации манифеста получателя может отвечать на осуществляет широковещательную рассылку, даже если приложение не запущено.  

Существует два набора API-интерфейсы для управления широковещательных приемника и отправке широковещательного:

1. **`Context`** &ndash; `Android.Content.Context` Класс может использоваться для регистрации широковещательных приемник, который будет отвечать на системные события. `Context` Также используется для публикации широковещательных рассылок во всей системе.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; API-интерфейс, который доступен через [пакет NuGet v4 библиотека поддержки Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Этот класс используется для сохранения широковещательных рассылок и широковещательных получателей, изолированных в контексте приложения, которое работает с ними. Этот класс может быть полезно для предотвращения отвечать на широковещательные рассылки только для приложений и отправки сообщений частной получателям других приложений.

Широковещательных приемник может отображаться диалоговые окна и его настоятельно не рекомендуется для запуска действия из вещания получателя. Если широковещательных получатель должен уведомить пользователя, его следует опубликовать уведомление.

Не поддерживается для привязки к или запустить службу в широковещательных получателя. 

В этом руководстве рассматривается создание широковещательных приемника и зарегистрировать его, чтобы она может получать широковещательные пакеты.

## <a name="creating-a-broadcast-receiver"></a>Создание приемника вещания

Создание широковещательных получателя Xamarin.Android, приложение должно подкласс `BroadcastReceiver` класса, его с оформления `BroadcastReceiverAttribute`и Переопределите `OnReceive` метод:

```csharp
[BroadcastReceiver(Enabled = true, Exported = false)]
public class SampleReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here.

        String value = intent.GetStringExtra("key");
    }
}
```

Когда Xamarin.Android компилирует класса, он также обновить AndroidManifest с meta данными, необходимыми для регистрации в приемник. Для широковещательных приемника статически зарегистрированы `Enabled` правильно, должно быть присвоено `true`, в противном случае Android не смогут создавать экземпляры приемника.
 
`Exported` Свойство управляет ли широковещательных получатель может получать сообщения извне приложения. Если свойство не задано явно, значение по умолчанию свойства определяется Android на основе, при наличии фильтры цель, связанные с широковещательных получателя. Если имеется хотя бы одного фильтра назначение для широковещательной рассылки получателя, а затем Android предполагается, что `Exported` свойство `true`. Если не намерение-фильтры, связанные с широковещательных получателем, то Android будет предполагать, что значение является `false`. 

`OnReceive` Метод получает ссылку на `Intent` , была подготовлена широковещательных приемник. Это делает возможна отправитель назначение для передачи значений широковещательных приемник.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>Статически регистрация приемником широковещательных пакетов с намерением фильтра

Когда `BroadcastReceiver` снабжен [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/), Xamarin.Android добавит необходимые `<intent-filter>` элемент Android манифеста во время компиляции. Следующий фрагмент кода приведен пример широковещательных приемника, который будет выполняться после завершения загрузки (если пользователю были предоставлены соответствующие разрешения Android) устройства.

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { Android.Content.Intent.ActionBootCompleted })]
public class MyBootReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Work that should be done when the device boots.     
    }
}
```

Можно также создание условий фильтра, который будет реагировать на настраиваемые цели. Рассмотрим следующий пример. 

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { "com.xamarin.example.TEST" })]
public class MySampleBroadcastReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here
    }
}
```

Приложения, предназначенные Android 8.0 (API уровня 26) или более поздней версии не может зарегистрировать статически неявное широковещательной рассылки. Приложения по-прежнему статически может зарегистрироваться для явного широковещательной рассылки. Имеется небольшой список неявных пакетов, которые будут исключены из этих ограничений. Эти исключения описаны в [неявное широковещательных исключения](https://developer.android.com/guide/components/broadcast-exceptions.html) руководство по документации по Android. Приложения, которые интересуют неявное широковещательных рассылок необходимо выполнить динамически с помощью `RegisterReceiver` метод. Это описано далее.

### <a name="context-registering-a-broadcast-receiver"></a>Регистрация контекста широковещательных получателя

Контекст регистрации (также называется динамической регистрации) приемника выполняется путем вызова `RegisterReceiver` метод и широковещательной рассылки получателя должны быть отменена с помощью вызова `UnregisterReceiver` метод. Чтобы предотвратить происходит утечка ресурсов, важно отменять регистрацию получателя, если он больше не нужны для контекста (действие или службы). Например служба может широковещательных пакетов целью для информирования действие, которое обновления становятся доступными для отображения пользователю. При запуске действия будет зарегистрирован для этих целей. Когда действие перемещается в фоновом режиме и больше не отображается для пользователя, его необходимо отменить регистрацию получателя, так как пользовательский Интерфейс для отображения обновлений больше не отображается. В следующем фрагменте кода приведен пример того, как регистрировать и отменять регистрацию широковещательных получателя в контексте действия:

```csharp
[Activity(Label = "MainActivity", MainLauncher = true, Icon = "@mipmap/icon")]
public class MainActivity: Activity 
{
    MySampleBroadcastReceiver receiver;

    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        receiver = new MySampleBroadcastReceiver()

        // Code omitted for clarity
    }

    protected override OnResume() 
    {
        base.OnResume();
        RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
        // Code omitted for clarity
    }

    protected override OnPause() 
    {
        UnregisterReceiver(receiver);
        // Code omitted for clarity
        base.OnPause();
    }
}
```

В предыдущем примере, когда действие поступает на переднем плане, будет зарегистрирован широковещательных приемник, который будет прослушивать пользовательских назначение с помощью `OnResume` метод жизненного цикла. Когда действие переносится в фоновом режиме, `OnPause()` метод будет отменена регистрация приемника.

## <a name="publishing-a-broadcast"></a>Публикация вещания

Вещания может быть опубликована для всех приложений, установленных на устройстве, для создания объекта, блокировка с намерением и распределения их с `SendBroadcast` или `SendOrderedBroadcast` метод.  

1. **Методы Context.SendBroadcast** &ndash; существует несколько реализаций этого метода.
   Эти методы будут разосланы цель для всей системы. Thatwill широковещательных получатели получают цель в неопределенном порядке. Это обеспечивает большую гибкость, но означает, что возможность другим приложениям для регистрации и получения цель. Это может представлять угрозу безопасности. Приложения может потребоваться реализовать добавление защиты для предотвращения несанкционированного доступа. Одно из возможных решений является использование `LocalBroadcastManager` которого будет только отправки сообщений в закрытое пространство приложения. Этот фрагмент кода является одним из примеров того, как отправлять цели, используя один из `SendBroadcast` методов:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   intent.PutExtra("key", "value");
   SendBroadcast(intent);
   ```

    В этом фрагменте еще один пример отправки вещания с помощью `Intent.SetAction` метод для идентификации действия:

    ```csharp 
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context.SendOrderedBroadcast** &ndash; этот метод является очень похоже на `Context.SendBroadcast`, отличие состоит, намерение будет опубликованных один во время получателям, в том порядке, что recievers были зарегистрированы.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

[V4 библиотека поддержки Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) предоставляет вспомогательный класс с именем [ `LocalBroadcastManager` ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html). `LocalBroadcastManager` Предназначен для приложений, которые вы хотите отправлять или получать широковещательные пакеты из других приложений на устройстве. `LocalBroadcastManager` Только будет публиковать сообщения в контексте приложения и только для этих широковещательных приемников, которые зарегистрированы с помощью `LocalBroadcastManager`. Этот фрагмент кода входит пример регистрации широковещательных приемника с `LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Другие приложения на устройстве не может получать сообщения, которые публикуются с `LocalBroadcastManager`. Этот фрагмент кода показывает, как для перенаправления с намерением с помощью `LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
intent.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Связанные ссылки

- [BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Context.RegisterReceiver](https://developer.xamarin.com/api/member/Android.Content.Context.RegisterReceiver/p/Android.Content.BroadcastReceiver/Android.Content.IntentFilter/System.String/Android.OS.Handler/)
- [Context.SendBroadcast](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/)
- [Context.UnregisterReceiver](https://developer.xamarin.com/api/member/Android.Content.Context.UnregisterReceiver/p/Android.Content.BroadcastReceiver/)
- [Назначение](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [IntentFilter](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
- [LocalBroadcastManager](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Локальный уведомления в Android](~/android/app-fundamentals/notifications/local-notifications.md)
- [Android поддерживает библиотеку v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
