---
title: Широковещательные приемники в Xamarin.Android
description: В этом разделе описывается использование широковещательного приемника.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 51bd3dd4c27dce19344f7660c31a0d4e741e1ad4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121143"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Широковещательные приемники в Xamarin.Android

_В этом разделе описывается использование широковещательного приемника._

## <a name="broadcast-receiver-overview"></a>Общие сведения о широковещательный приемник

Объект _широковещательный приемник_ — это Android компонент, который позволяет приложению реагировать на сообщения (Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)), передаваемого Android операционной системой или приложением. Выполните широковещательных рассылок _публикации и подписки_ модели &ndash; событие вызывает вещания для публикации и полученных те компоненты, которые заинтересованы в событии.

Android определяет два типа пакетов:

* **Явные вещания** &ndash; этих типов широковещательного предназначенных для конкретного приложения. Явные вещания чаще всего используется для запуска действия. Пример явной вещания, когда приложению нужен для набора номера телефона; она передает объект Intent, ориентированном на Android и pass вдоль номер телефона, устанавливать приложение. В приложение для телефона Android будет перенаправлять цель.
* **Неявные вещания** &ndash; презентации доставлены ко всем приложениям на устройстве. Например неявные вещания, `ACTION_POWER_CONNECTED` намерение. В этой описаны публикуется каждый раз, когда Android обнаруживает, что на устройстве зарядки. Android будет маршрутизации этого намерения ко всем приложениям, которые зарегистрированы для данного события.

Широковещательный получатель является подклассом `BroadcastReceiver` типа и его необходимо переопределить [ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/) метод. Android будет выполняться `OnReceive` в основном потоке, поэтому этот метод должны разрабатываться выполняться быстро. Следует соблюдать осторожность при создании процесса потоков в `OnReceive` потому, что Android может завершить процесс при завершении метода. Если широковещательный получатель должен выполнять длительных рабочих, то рекомендуется запланировать _задания_ с помощью `JobScheduler` или _диспетчер заданий Firebase_. Планирование работы с заданием будут рассмотрены отдельные руководства.

_Фильтром намерений_ используется для регистрации широковещательный приемник Android правильной передачи сообщений. Во время выполнения можно указать фильтр намерений (это иногда называется _контекст зарегистрирован получателя_ или как _динамической регистрации_) или он может быть статически определен в манифесте Android ( _регистрации манифеста получателя_). Xamarin.Android предоставляет C# атрибут, `IntentFilterAttribute`, которая статически зарегистрирует фильтр намерений (это будет рассматриваться более подробно далее в этом руководстве). Начиная с Android 8.0, он не поддерживается для приложения для статически регистрации для неявного вещания.

Основное различие между регистрации манифеста получателя, а получатель контекст зарегистрирован —, что получатель контекст зарегистрирован будет отвечать только на широковещательный трафик во время работы приложения, пока получатель зарегистрированный манифест может отвечать на осуществляет широковещательную передачу, несмотря на то, что приложение может не работать.  

Существует два набора API для отправки широковещательных рассылок и управления ими широковещательный приемник.

1. **`Context`** &ndash; `Android.Content.Context` Класс может использоваться для регистрации широковещательный получатель, который будет реагировать на события уровня системы. `Context` Также используется для публикации широковещательных рассылок всей системы.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; API-интерфейс, который доступен через [пакет NuGet библиотеки поддержки Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Этот класс используется для вещания и широковещательных получателей, изолированы в контексте приложения, которое их использует. Этот класс может быть полезно для предотвращения отвечать на широковещательные рассылки только для приложений и отправки сообщений в приемники, закрытый других приложений.

Широковещательный получатель не может отображать диалоговые окна, и это настоятельно не рекомендуется, чтобы начать действия из широковещательных получателей. Если широковещательный получатель должен уведомлять пользователя, его следует опубликовать уведомление.

Не поддерживается для привязки к или запустить службу в широковещательный приемник. 

В этом руководстве рассматриваются способы создания широковещательный приемник и зарегистрировать его, чтобы оно может получить широковещательных рассылок.

## <a name="creating-a-broadcast-receiver"></a>Создание получателя рассылки

Чтобы создать широковещательный приемник в Xamarin.Android, приложение должно подкласс `BroadcastReceiver` класса, создавать его с `BroadcastReceiverAttribute`и Переопределите `OnReceive` метод:

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

Когда Xamarin.Android компилирует класс, он также будет обновлен AndroidManifest необходимые метаданные для регистрации приемника. Для статически зарегистрированные широковещательный приемник `Enabled` правильно, должно быть присвоено `true`, в противном случае Android будет невозможно создать экземпляр приемника.
 
`Exported` Свойство управляет ли широковещательный получатель может получать сообщения из вне приложения. Если свойство не задано явным образом, значение по умолчанию свойства определяется на основе при наличии фильтры намерения, связанные с широковещательный приемник Android. Если есть хотя бы один фильтр намерения для широковещательных получателей, а затем Android предполагается, что `Exported` свойство `true`. Если не намерение-фильтры, связанные с широковещательных получателей, то Android предполагает, что значение является `false`. 

`OnReceive` Метод получает ссылку на `Intent` , был отправлен для широковещательных получателей. В результате возможна для отправителя способа для передачи значений широковещательных получателей.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>Статически регистрация широковещательного приемника с фильтром намерение

Когда `BroadcastReceiver` снабжен [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/), Xamarin.Android для добавления необходимой `<intent-filter>` элемент Android манифеста во время компиляции. Ниже приведен пример широковещательный приемник, который будет выполняться после завершения загрузки (если пользователю были предоставлены соответствующие разрешения Android) устройства:

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

Можно также создать фильтр намерений, будут отвечать на настраиваемые цели. Рассмотрим следующий пример. 

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

Приложений, предназначенных для Android 8.0 (уровень API 26) или более поздней версии не может зарегистрировать статически неявное широковещательной передачи. Приложения могут по-прежнему статически регистрироваться для явной широковещательной передачи. Имеется небольшой список неявных-пакетов, которые будут исключены из этих ограничений. Эти исключения описаны в [неявное широковещательных исключения](https://developer.android.com/guide/components/broadcast-exceptions.html) руководство в документации по Android. Приложения, которые хотели бы неявное широковещательные рассылки необходимо выполнить динамически с помощью `RegisterReceiver` метод. Это описано далее.

### <a name="context-registering-a-broadcast-receiver"></a>Регистрация контекста широковещательный приемник

Контекст регистрации (также называется динамической регистрации) приемника выполняется путем вызова `RegisterReceiver` метод и широковещательных получателей должен выполнить отмену регистрации с вызовом `UnregisterReceiver` метод. Чтобы предотвратить происходит утечка ресурсов, важно отменять регистрацию получателя, если он больше не нужны для контекста (действие или службы). Например служба может широковещательный объекта intent, чтобы указать действие, которое будет отображаться для пользователя доступны обновления. В начале действия его зарегистрировать для этих целей. Когда действие перемещается в фоновом режиме, и больше не отображается для пользователя, необходимо отменить регистрацию получателя, так как пользовательский Интерфейс для отображения обновлений больше не отображается. В следующем фрагменте кода приведен пример того, как регистрировать и отменять регистрацию широковещательный приемник в контексте действия:

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

В предыдущем примере, когда действие поступает на переднем плане, он будет зарегистрирован широковещательный приемник, который будет прослушивать пользовательских намерение с помощью `OnResume` метод жизненного цикла. Действия при перемещении в фоновом режиме, `OnPause()` метод будет отменена регистрация получателя.

## <a name="publishing-a-broadcast"></a>Публикация вещания

Вещание могут быть опубликованы все приложения, установленные на устройстве, создавая объект намерения и распределения их с `SendBroadcast` или `SendOrderedBroadcast` метод.  

1. **Методы Context.SendBroadcast** &ndash; существует несколько реализаций этого метода.
   Эти методы будут разосланы целью всей системы. Широковещательные приемники, которые будут получать цель в неопределенном порядке. Это обеспечивает значительную гибкость, но означает, что другие приложения могут зарегистрироваться и получать цель. Это может представлять угрозу безопасности. Приложения, нужно реализовать сложения безопасности для предотвращения несанкционированного доступа. Одно из возможных решений является использование `LocalBroadcastManager` которого будет только перенаправления сообщений, отправляемых в закрытый пространстве приложения. В этом фрагменте кода приведен пример того, как для отправки объекта intent, с помощью одного из `SendBroadcast` методы:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    В этом фрагменте приведен еще один пример отправки вещания с помощью `Intent.SetAction` метод для идентификации действия:

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context.SendOrderedBroadcast** &ndash; это метод очень похожа на `Context.SendBroadcast`, с разницей, что цель будет опубликованных во время для приемников, в том порядке, что получатели были зарегистрированы.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

[V4 библиотека поддержки Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) предоставляет вспомогательный класс с именем [ `LocalBroadcastManager` ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html). `LocalBroadcastManager` Предназначен для приложений, которые не хотите отправлять или получать широковещательные пакеты из других приложений на устройстве. `LocalBroadcastManager` Только будет публиковать сообщения в контексте приложения и только для этих широковещательных получателей, которые зарегистрированы в `LocalBroadcastManager`. В этом фрагменте кода приведен пример регистрации широковещательный приемник с `LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Другие приложения на устройстве не может принимать сообщения, которые публикуются с `LocalBroadcastManager`. В этом фрагменте кода показано, как для отправки в намерения с помощью `LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Связанные ссылки

- [BroadcastReceiver API](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Context.RegisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.RegisterReceiver/p/Android.Content.BroadcastReceiver/Android.Content.IntentFilter/System.String/Android.OS.Handler/)
- [Context.SendBroadcast API](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/)
- [Context.UnregisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.UnregisterReceiver/p/Android.Content.BroadcastReceiver/)
- [Блокировка намерения API](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [IntentFilter API](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
- [LocalBroadcastManager (Android документация)](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Локальные уведомления в Android](~/android/app-fundamentals/notifications/local-notifications.md) руководство
- [Поддержки Android v4 библиотеки (NuGet)](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
