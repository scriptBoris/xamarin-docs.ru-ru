---
title: Связанные службы в Xamarin.Android
description: Связанные службы являются Android служб, которые обеспечивают интерфейс клиент сервер, который может взаимодействовать клиент (например, Android действия). В этом руководстве обсуждаются основные компоненты, связанной с созданием привязанной службы и способ его использования в приложении Xamarin.Android.
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 05/04/2018
ms.openlocfilehash: f4fe1bd753260f05dedb452655572d290c0781d0
ms.sourcegitcommit: daa089d41cfe1ed0456d6de2f8134cf96ae072b1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33850578"
---
# <a name="bound-services-in-xamarinandroid"></a>Связанные службы в Xamarin.Android

_Связанные службы являются Android служб, которые обеспечивают интерфейс клиент сервер, который может взаимодействовать клиент (например, Android действия). В этом руководстве обсуждаются основные компоненты, связанной с созданием привязанной службы и способ его использования в приложении Xamarin.Android._

## <a name="bound-services-overview"></a>Общие сведения о связанных службах

Службы, предоставляемые интерфейсом клиент сервер, чтобы клиенты могли взаимодействовать напрямую со службой, называются _привязан службы_.  Может существовать несколько клиентов, подключенных к одному экземпляру службы в то же время. Связанные службы и клиента изолированы друг от друга. Вместо этого Android предоставляет ряд промежуточных объектов, которые управляют состояние соединения между двумя. Это состояние, поддерживаемое объектом, реализующим [ `Android.Content.IServiceConnection` ](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/) интерфейса.  Этот объект создан с помощью клиента и передавать в качестве параметра для [ `BindService` ](https://developer.xamarin.com/api/member/Android.Content.Context.BindService/) метод. `BindService` Доступно на любом [ `Android.Content.Context` ](https://developer.xamarin.com/api/type/Android.Content.Context) объекта (например, действия). Данный запрос для операционной системы Android для запуска службы и привязку клиента к нему. Существует три способа клиента выполнить привязку службы с помощью `BindService` метод:

* **Связыватель службы** &ndash; связыватель службы — это класс, реализующий [ `Android.OS.IBinder` ](https://developer.xamarin.com/api/type/Android.OS.IBinder) интерфейса. Большинство приложений не будет реализовывать этот интерфейс непосредственно, вместо этого они расширяют [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) класса. Это наиболее распространенный подход и подходит для при службы и клиента существует в том же процессе.
* **С помощью программы Messenger** &ndash; этот способ подходит для при служба может размещаться в отдельном процессе. Вместо этого запросы на обслуживание маршалировать между клиентом и службой через [ `Android.OS.Messenger` ](https://developer.xamarin.com/api/type/Android.OS.Messenger). [ `Android.OS.Handler` ](https://developer.xamarin.com/api/type/Android.OS.Handler) Создается в службу, которая будет обрабатывать `Messenger` запросов. Это будет рассматриваться в другой руководства.
* **С помощью AIDL** &ndash; это сохранилась террористических в лежит в основе большинство разработчиков Android и не будет рассматриваться в данном руководстве.

После привязки клиента в службу обмена данными между ними является выполняется с помощью `Android.OS.IBinder` объекта.  Этот объект отвечает за интерфейс, который позволяет клиенту взаимодействовать со службой. Это необходимо для каждого приложения Xamarin.Android для реализации этого интерфейса с нуля, предоставляет пакет SDK Android [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) класс, который отвечает за большую часть кода, необходимые в связи с маршалинга объекта между Клиент и служба.

Закончив клиента со службой, его необходимо отменить привязку из него путем вызова `UnbindService` метод. После последнего клиента содержит несвязанные из службы, Android остановить и удалить связанные службы.

На этой диаграмме показано, как действия, подключения службы, привязки и службы все связаны друг с другом.

![Схема, показывающая, как компоненты службы связаны друг с другом](bound-services-images/bound-services-02.png "схема, показывающая, как компоненты службы связаны друг с другом.")

В этом руководстве описывается, как расширить `Service` класса для реализации привязанной службы. Также рассматривается реализация `IServiceConnection` и расширение `Binder` позволяет клиенту взаимодействовать со службой. Пример приложения сопровождающий это руководство, которой содержат решения с именем одного проекта Xamarin.Android **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** . Это очень простое приложение, который демонстрирует реализацию службы и привязать действие к нему. Связанные службы имеет очень простой API с только один метод `GetFormattedTimestamp`, который возвращает строку, которая информирует пользователя, если служба запущена и срок ее выполнения. Приложение также позволяет пользователю вручную отменить привязку и привязки к службе.

[![Снимок экрана приложения, запущенного на телефоне с Android](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>Реализация и использование связанные службы

Состоит из трех компонентов, которые должны быть реализованы в порядке для приложения Android для использования связанные службы:

1. **Расширить `Service` класса и реализовать методы жизненного цикла обратного вызова** &ndash; этот класс будет содержать код, который будет выполнять свою работу службы, будет запрошен. Рассматриваются более подробно ниже.
2. **Создайте класс, реализующий `IServiceConnection`**  &ndash; этот интерфейс предоставляет методы обратного вызова будет вызван системой Android для уведомления клиента, при изменении подключения к службе, т. е. клиент подключен отсоединен или для Служба. Кроме того, подключения службы предоставляет ссылку на объект, который клиент может использовать для непосредственного взаимодействия со службой. Эта ссылка называется _связыватель_.
3. **Создайте класс, реализующий `IBinder`**  &ndash; A _связыватель_ реализация предоставляет API, который клиент использует для взаимодействия со службой. Связыватель может либо ссылку на привязанную службу обеспечивает методы для вызова непосредственно связывателем задать либо клиентских API, который инкапсулирует и скрывает привязанной службы из приложения. `IBinder` Должен предоставлять необходимый код для удаленных вызовов процедур. Это не требуется (или не рекомендуется) для реализации `IBinder` интерфейс непосредственно. Вместо этого приложения должен расширять `Binder` тип, который предоставляет большую часть базовой функции, необходимые для `IBinder`.
4. **Запуск и привязка к службе** &ndash; после создания подключения службы, привязки и службы приложения Android отвечает за запуск службы и привязка к нему.

Каждое из этих действий обсуждаются в следующих разделах более подробно.

### <a name="extend-the-service-class"></a>Расширить `Service` класса

Чтобы создать службу, с помощью Xamarin.Android, необходимо создать подкласс `Service` и для оформления класса с [ `ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute). Атрибут используется средства построения Xamarin.Android правильно зарегистрировать службу в приложении **AndroidManifest.xml** файла очень похоже на действия, связанные службы имеет собственный жизненный цикл и обратного вызова методов, связанных с значимые события его жизненного цикла. Ниже приведен пример некоторые из наиболее распространенных методов обратного вызова, реализуемых службой.

* `OnCreate` &ndash; Этот метод вызывается системой Android, как он создание экземпляров службы. Он позволяет инициализировать переменные или объекты, которые требуются службы во время его существования. Этот метод является необязательным.
* `OnBind` &ndash; Этот метод должен быть реализован во всех связанных службах. Вызывается, когда первый клиент пытается подключиться к службе. Возвращает экземпляр `IBinder` , чтобы клиент может взаимодействовать со службой. При условии, что служба запущена, `IBinder` объект будет использоваться для выполнения все последующие клиентские запросы, для привязки к службе.
* `OnUnbind` &ndash; Этот метод вызывается, когда все связанные клиенты отсутствует привязка. Путем возвращения `true` из этого метода позже будет вызывать службу `OnRebind` с целью передаваемый `OnUnbind` при новые клиенты привязки к нему. Это делается при служба продолжает работу после ее свободной. Это может произойти, если недавно несвязанного службы также были запущенной службы и `StopService` или `StopSelf` еще не был вызван. В этом случае `OnRebind` позволяет намерение требуется получить. Возвращает значение по умолчанию `false` , который не выполняет никаких действий. Необязательный.
* `OnDestroy` &ndash; Этот метод вызывается, когда Android уничтожение службы. В этом методе необходимо выполнить необходимую очистку, такие как освобождение ресурсов. Необязательный.

На этой диаграмме показаны события жизненного цикла ключа связанные службы:

![Схема, показывающая порядок вызова методов жизненного цикла](bound-services-images/bound-services-01.png "схема, показывающая порядок вызова методов жизненного цикла.")

Дополнительное приложение, сопровождающий это руководство, в следующем фрагменте кода показано, как реализовать службу привязанной в Xamarin.Android:

```csharp
using Android.App;
using Android.Util;
using Android.Content;
using Android.OS;

namespace BoundServiceDemo
{
    [Service(Name="com.xamarin.ServicesDemo1")]
    public class TimestampService : Service, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampService).FullName;
        IGetTimestamp timestamper;

        public IBinder Binder { get; private set; }

        public override void OnCreate()
        {
            // This method is optional to implement
            base.OnCreate();
            Log.Debug(TAG, "OnCreate");
            timestamper = new UtcTimestamper();
        }

        public override IBinder OnBind(Intent intent)
        {
            // This method must always be implemented
            Log.Debug(TAG, "OnBind");
            this.Binder = new TimestampBinder(this);
            return this.Binder;
        }

        public override bool OnUnbind(Intent intent)
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnUnbind");
            return base.OnUnbind(intent);
        }

        public override void OnDestroy()
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnDestroy");
            Binder = null;
            timestamper = null;
            base.OnDestroy();
        }

        /// <summary>
        /// This method will return a formatted timestamp to the client.
        /// </summary>
        /// <returns>A string that details what time the service started and how long it has been running.</returns>
        public string GetFormattedTimestamp()
        {
            return timestamper?.GetFormattedTimestamp();
        }
    }
}
```

В образце `OnCreate` метод инициализирует объект, который содержит логику для извлечения и форматирование отметкой времени, которая будет запрашиваться клиентом. Если первый клиент пытается выполнить привязку к службе, будут вызывать Android `OnBind` метод. Эта служба будет создан экземпляр `TimestampServiceBinder` объект, который позволит клиентам доступ к данному экземпляру запущенной службы. `TimestampServiceBinder` Класс рассматривается в следующем разделе.

### <a name="implementing-ibinder"></a>Реализация IBinder

Как уже упоминалось, `IBinder` объект предоставляет канал связи между клиентом и службой. Не следует реализовывать приложения Android `IBinder` интерфейс, [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder/) должен быть расширен. `Binder` Класс предоставляет большую часть необходимую инфраструктуру, являющийся необходимости маршалинг связыватель клиенту от службы (который может выполняться в отдельном процессе). В большинстве случаев `Binder` подкласс имеет всего несколько строк кода и создает оболочку для ссылки на службу. В этом примере `TimestampBinder` имеет свойство, которое предоставляет `TimestampService` клиенту:

```csharp
public class TimestampBinder : Binder
{
    public TimestampBinder(TimestampService service)
    {
        this.Service = service;
    }

    public TimestampService Service { get; private set; }
}
```

Это `Binder` делает возможным для вызова открытых методов в службе; например:

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

Один раз `Binder` была расширена, требуется реализовать `IServiceConnection` соединять все данные.

### <a name="creating-the-service-connection"></a>Создание подключения службы

`IServiceConnection` Появятся | вводят | предоставлять | подключения `Binder` объект клиенту. Помимо реализации `IServiceConnection` интерфейса, необходимо расширить класс `Java.Lang.Object`. Подключение службы должен предоставлять способом, который клиент может обращаться к `Binder` (и, таким образом взаимодействуют со службой связанных).

Этот фрагмент кода взят из сопроводительный пример проекта — один из возможных способов реализации `IServiceConnection`:

```csharp
using Android.Util;
using Android.OS;
using Android.Content;

namespace BoundServiceDemo
{
    public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampServiceConnection).FullName;

        MainActivity mainActivity;
        public TimestampServiceConnection(MainActivity activity)
        {
            IsConnected = false;
            Binder = null;
            mainActivity = activity;
        }

        public bool IsConnected { get; private set; }
        public TimestampBinder Binder { get; private set; }

        public void OnServiceConnected(ComponentName name, IBinder service)
        {
            Binder = service as TimestampBinder;
            IsConnected = this.Binder != null;

            string message = "onServiceConnected - ";
            Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

            if (IsConnected)
            {
                message = message + " bound to service " + name.ClassName;
                mainActivity.UpdateUiForBoundService();
            }
            else
            {
                message = message + " not bound to service " + name.ClassName;
                mainActivity.UpdateUiForUnboundService();
            }

            Log.Info(TAG, message);
            mainActivity.timestampMessageTextView.Text = message;

        }

        public void OnServiceDisconnected(ComponentName name)
        {
            Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
            IsConnected = false;
            Binder = null;
            mainActivity.UpdateUiForUnboundService();
        }

        public string GetFormattedTimestamp()
        {
            if (!IsConnected)
            {
                return null;
            }

            return Binder?.GetFormattedTimestamp();
        }
    }
}

```

В рамках процесса привязки будет вызывать Android `OnServiceConnected` метод, предоставляя `name` службы, к которому осуществляется привязка и `binder` , хранящий ссылку на саму службу. В этом примере подключения службы имеет два свойства, содержит ссылку на связыватель и логический флаг для, если клиент подключен к службе или нет.

`OnServiceDisconnected` Метод вызывается только в том случае, когда неожиданно потерян или разрыв соединения между клиентом и службой. Этот метод позволяет клиенту возможность реагировать на время прерывания работы службы.  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>Запуск и привязка к службе с целью явные

Чтобы использовать связанные службы, клиент (например, действия) необходимо создать экземпляр объекта, реализующего `Android.Content.IServiceConnection` и вызова `BindService` метод.` BindService` Возвращает `true` Если служба привязана к, `false` Если это не так. Метод `BindService` принимает три следующих параметра.

* **`Intent`**  &ndash; Намерение следует явным образом указывать какие службы для подключения к.
* **`IServiceConnection` Объекта** &ndash; этого объекта — это посредник, который предоставляет методы обратного вызова для уведомления клиента, при остановке и запуске привязанной службы.
* **[`Android.Content.Bind`](https://developer.xamarin.com/api/type/Android.Content.Bind/) Перечисление** &ndash; этот параметр является набор флагов, используемые системой при привязать объект. Наиболее часто используемые значение [ `Bind.AutoCreate` ](https://developer.xamarin.com/api/field/Android.Content.Bind.AutoCreate/), который будет автоматически запускает службу, если она еще не запущена.

В следующем фрагменте кода приведен пример инструкции по запуску привязанной службы в действие с помощью явного целью.

```csharp
protected override void OnStart ()
{
    base.OnStart ();

    if (serviceConnection == null)
    {
        this.serviceConnection = new TimestampServiceConnection(this);
    }

    Intent serviceToStart = new Intent(this, typeof(TimestampService));
    BindService(serviceToStart, this.serviceConnection, Bind.AutoCreate);

}
```

> [!IMPORTANT]
> Начиная с Android 5.0 (уровень API 21) поддерживается только для привязки к службе с целью явной.

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>Архитектурные замечания о соединении и средство привязки.

Некоторые purists объектно-ориентированное Программирование может отклонить предыдущей реализации `TimestampBinder` класса как нарушение [закона](https://en.wikipedia.org/wiki/Law_of_Demeter) , в простейшей форме указывается «не общаться с незнакомыми пользователями; только общаться с друзьями». Эта конкретная реализация предоставляет конкретных `TimestampService` класс для всех клиентов.

Строго говоря, нет необходимости для клиента для получения сведений о `TimestampService` и предоставление этого конкретного класса клиентам осуществлять приложения более тонкой и к усложнению за время его существования. Альтернативный подход заключается в использовании интерфейс, который предоставляет `GetFormattedTimestamp()` метода и вызовы прокси-сервера службы через `Binder` (или возможные в классе службы подключения):  

```csharp
public class TimestampBinder : Binder, IGetTimestamp
{
    TimestampService service;
    public TimestampBinder(TimestampService service)
    {
        this.service = service;
    }

    public string GetFormattedTimestamp()
    {
        return service?.GetFormattedTimestamp();
    }
}
```

Данном конкретном примере позволяют действию для вызова методов в самой службы:

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```


## <a name="related-links"></a>Связанные ссылки

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.Content.Bind](https://developer.xamarin.com/api/type/Android.Content.Bind/)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [Android.Content.IServiceConnection](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)
- [Android.OS.Binder](https://developer.xamarin.com/api/type/Android.OS.Binder/)
- [Android.OS.IBinder](https://developer.xamarin.com/api/type/Android.OS.IBinder)
- [BoundServiceDemo (пример)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/BoundServiceDemo/)
