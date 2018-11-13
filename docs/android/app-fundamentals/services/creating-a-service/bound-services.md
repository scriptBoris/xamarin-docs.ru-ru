---
title: Службы привязки в Xamarin.Android
description: Связанные службы являются Android службы, обеспечивающие интерфейс между клиентом и сервером, который может взаимодействовать клиент (например, действие Android). В этом руководстве будут рассмотрены ключевые компоненты, связанные с созданием привязанную службу и способы ее использования в приложениях Xamarin.Android.
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/04/2018
ms.openlocfilehash: c0adee0dae1135bdfd076082e85a471db1cd1ecf
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528563"
---
# <a name="bound-services-in-xamarinandroid"></a>Службы привязки в Xamarin.Android

_Связанные службы являются Android службы, обеспечивающие интерфейс между клиентом и сервером, который может взаимодействовать клиент (например, действие Android). В этом руководстве будут рассмотрены ключевые компоненты, связанные с созданием привязанную службу и способы ее использования в приложениях Xamarin.Android._

## <a name="bound-services-overview"></a>Общие сведения о связанных службах

Службы, которые предоставляют интерфейс между клиентом и сервером для клиентов, для непосредственного взаимодействия со службой, называются _службы привязки_.  Может существовать несколько клиентов, подключенных к одному экземпляру службы, в то же время. Связанные службы и клиента изолированы друг от друга. Вместо этого Android предоставляет ряд промежуточных объектов, которые управляют состояние соединения между ними. Это состояние сохраняется в объекте, который реализует [ `Android.Content.IServiceConnection` ](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/) интерфейс.  Этот объект создается с помощью клиента и передается в качестве параметра [ `BindService` ](https://developer.xamarin.com/api/member/Android.Content.Context.BindService/) метод. `BindService` Доступно на любом [ `Android.Content.Context` ](https://developer.xamarin.com/api/type/Android.Content.Context) объекта (например, действие). Это запрос на ОС Android для запуска службы и привязку клиента к нему. Существует три способа клиента выполнить привязку к службе с помощью `BindService` метод:

* **Связыватель службы** &ndash; связыватель службы — это класс, реализующий [ `Android.OS.IBinder` ](https://developer.xamarin.com/api/type/Android.OS.IBinder) интерфейс. Большинство приложений не будет реализовывать этот интерфейс напрямую, вместо этого они будут расширены [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) класса. Это — наиболее распространенный подход и подходит для когда служба и клиент существует в том же процессе.
* **С помощью Messenger** &ndash; этот способ подходит для при службы могут существовать в отдельном процессе. Вместо этого запросы на обслуживание маршалируется между клиентом и службой через [ `Android.OS.Messenger` ](https://developer.xamarin.com/api/type/Android.OS.Messenger). [ `Android.OS.Handler` ](https://developer.xamarin.com/api/type/Android.OS.Handler) Создается в службе, которая будет обрабатывать `Messenger` запросов. Будут рассмотрены в другом руководстве.
* **С помощью Android языка определения интерфейса (AIDL)** &ndash; [AIDL](https://developer.android.com/guide/components/aidl) — это сложная процедура, не будут рассматриваться в данном руководстве.

После привязки клиента к службе, является взаимодействие между ними происходит с помощью `Android.OS.IBinder` объекта.  Этот объект отвечает за интерфейс, который позволит клиенту взаимодействовать со службой. Это необходимо для каждого приложения Xamarin.Android для реализации этого интерфейса с нуля, предоставляет пакет SDK для Android [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) класс, который берет на себя большую часть кода, необходимые в связи с маршалинга объекта между Клиент и служба.

Когда клиент выполняется в службе, его необходимо отменить существующую из него путем вызова `UnbindService` метод. Когда последний клиент имеет несвязанные из службы, Android остановит и ликвидирует привязки службы.

Схема иллюстрирует, как действие, подключение к службе, связыватель и службы все связанные друг с другом:

![Схема, демонстрирующая, как компоненты службы связаны друг с другом](bound-services-images/bound-services-02.png "схема, демонстрирующая, как компоненты службы связаны друг с другом.")

В этом руководстве описано, как расширить `Service` класс для реализации привязанную службу. Кроме того, рассматривается реализация `IServiceConnection` и расширение `Binder` разрешающее клиенту обмениваться данными со службой. Пример приложения прилагается к в этом руководстве, которые содержат решения с именем одного проекта Xamarin.Android **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** . Это очень простое приложение, в которой показано, как реализовать службу и как привязать действие к нему. Связанные службы имеет очень простой API-Интерфейс с только одним методом, `GetFormattedTimestamp`, который возвращает строку, которая сообщает пользователю, когда служба запущена и сколько времени его выполнения. Приложение также позволяет пользователю вручную отменить привязку и привязки к службе.

[![Снимок экрана приложения, работающие на телефоне с Android](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>Реализация и использование связанные службы

Существует три компонента, которые должны быть реализованы в приложение Android для использования привязки службы.

1. **Расширить `Service` класса и реализовать методы обратного вызова жизненного цикла** &ndash; этот класс будет содержать код, который выполнит действия, который будет запрашиваться службы. Это будет рассматриваться более подробно ниже.
2. **Создайте класс, реализующий `IServiceConnection`**  &ndash; этот интерфейс предоставляет методы обратного вызова будет вызвана с Android для уведомления клиента, при изменении подключения к службе, т. е. клиент подключением и отключением для Служба. Подключения службы также предоставит ссылку на объект, который клиент может использовать для непосредственного взаимодействия со службой. Эта ссылка называется _связыватель_.
3. **Создайте класс, реализующий `IBinder`**  &ndash; объект _связыватель_ реализация предоставляет API, который клиент использует для взаимодействия со службой. Связыватель может предоставить ссылку на привязанную службу, позволяя методы, которые вызываются напрямую или связыватель может предоставить клиентским API, который инкапсулирует и скрывает привязанную службу из приложения. `IBinder` Должен предоставлять необходимый код для удаленных вызовов процедур. Это не требуется (или не рекомендуется) для реализации `IBinder` взаимодействует напрямую. Вместо этого приложения должен расширять `Binder` тип, предоставляющий большую часть базовой функциональности, необходимой `IBinder`.
4. **Запуск и привязка к службе** &ndash; после создания подключения службы, привязки и службы приложения Android отвечает за запуск службы и привязка к нему.

Каждое из этих действий будут рассмотрены в следующих разделах более подробно.

### <a name="extend-the-service-class"></a>Расширить `Service` класса

Чтобы создать службы с помощью Xamarin.Android, бывает необходимо подкласс `Service` и Декорирование класса с [ `ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute). Атрибут используется средствами сборки Xamarin.Android правильно зарегистрировать службу в приложении **AndroidManifest.xml** файл очень похоже на действие, привязанную службу имеет собственный жизненный цикл и обратного вызова методов, связанных с важные события в его жизненный цикл. Ниже приведен пример некоторые из наиболее распространенных методов обратного вызова, которые служба будет реализовать.

* `OnCreate` &ndash; Этот метод вызывается системой Android, как он создание экземпляра службы. Он используется для инициализации все переменные и объекты, которые требуются службе во время его существования. Этот метод является необязательным.
* `OnBind` &ndash; Этот метод должен быть реализован всех связанных служб. Он вызывается, когда первый клиент пытается подключиться к службе. Он вернет экземпляр `IBinder` таким образом, клиент может взаимодействовать со службой. До тех пор, пока служба запущена, `IBinder` объекта будет использоваться для выполнения все последующие запросы клиента выполнить привязку к службе.
* `OnUnbind` &ndash; Этот метод вызывается, когда все связанные клиенты несвязанных. Путем возвращения `true` из этого метода, позже будет вызывать службу `OnRebind` с целью передается `OnUnbind` при новые клиенты привязки к нему. Это делается при службы длится после ее не связанного с данными. Это может произойти, если недавно несвязанного службы также были работы службы, и `StopService` или `StopSelf` еще не был вызван. В этом случае `OnRebind` позволяет намерение требуется получить. Возвращает значение по умолчанию `false` , который не выполняет никаких действий. Необязательный.
* `OnDestroy` &ndash; Этот метод вызывается, когда Android уничтожение службы. Необходимые операции очистки, например для освобождения ресурсов, должны выполняться в этом методе. Необязательный.

На этой схеме показаны события жизненный цикл ключа связанные службы:

![Схема, демонстрирующая порядок, в котором вызываются методы жизненного цикла](bound-services-images/bound-services-01.png "схема, демонстрирующая порядок, в котором вызываются методы жизненного цикла.")

В следующем фрагменте кода из приложения companion, сопровождающий в этом руководстве показан способ реализации привязанную службу в Xamarin.Android:

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

В этом примере `OnCreate` метод инициализирует объект, который содержит логику для извлечения и форматирования отметки времени, который может запрашиваться клиентом. Если первый клиент пытается осуществить привязку к службе, будут вызывать Android `OnBind` метод. Эта служба будет создавать `TimestampBinder` объект, который позволит клиентам доступ к данному экземпляру, запущенной службы. `TimestampBinder` Класс рассматривается в следующем разделе.

### <a name="implementing-ibinder"></a>Реализация IBinder

Как уже упоминалось, `IBinder` объект обеспечивает канал связи между клиентом и службой. Приложения Android не должны реализовывать `IBinder` интерфейс, [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder/) должно быть воспроизведено. `Binder` Класс предоставляет большую часть необходимую инфраструктуру, который является необходимости маршалинг связыватель объект из службы (которое может выполняться в отдельном процессе) клиенту. В большинстве случаев `Binder` подкласс только несколько строк кода и создает оболочку для ссылки на службу. В этом примере `TimestampBinder` имеет свойство, которое предоставляет `TimestampService` клиенту:

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

Это `Binder` дает возможность вызова открытых методов в службе; например:

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

Один раз `Binder` была расширена, она необходима для реализации `IServiceConnection` для подключения все вместе.

### <a name="creating-the-service-connection"></a>Создание подключения службы

`IServiceConnection` Представит | вводят | предоставлять | подключения `Binder` объект клиенту. Помимо реализации `IServiceConnection` интерфейс, необходимо расширить класс `Java.Lang.Object`. Подключения службы также следует предоставить таким способом, который клиент может получить доступ к `Binder` (и таким образом взаимодействовать с привязанную службу).

Этот код взят из сопутствующий — это один из возможных способов реализации `IServiceConnection`:

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

Как часть процесса привязки, вызовет Android `OnServiceConnected` метод, предоставляя `name` службы, к которому осуществляется привязка и `binder` , хранящий ссылку на саму службу. В этом примере подключения службы имеет два свойства: один, содержащая ссылку на связыватель и логический флаг для в том случае, если клиент подключен к службе или нет.

`OnServiceDisconnected` Метод вызывается только в том случае, когда соединение между клиентом и службой является неожиданно потери или поломки. Этот метод позволяет клиенту возможность реагировать на время прерывания работы службы.  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>Запуск и привязка к службе с явной целью

Чтобы использовать связанные службы, клиент (например, действие) необходимо создать экземпляр объекта, реализующего `Android.Content.IServiceConnection` и вызвать `BindService` метод.` BindService` Возвращает `true` Если служба привязана к, `false` Если это не так. Метод `BindService` принимает три следующих параметра.

* **`Intent`**  &ndash; Намерение необходимо явно определить какая служба для подключения к.
* **`IServiceConnection` Объект** &ndash; этот объект — это посредник, который предоставляет методы обратного вызова для уведомления клиента, когда запущено и остановлено привязанную службу.
* **[`Android.Content.Bind`](https://developer.xamarin.com/api/type/Android.Content.Bind/) Перечисление** &ndash; этот параметр имеет набор флагов используются системой для при привязки объекта. Наиболее часто используемые значение [ `Bind.AutoCreate` ](https://developer.xamarin.com/api/field/Android.Content.Bind.AutoCreate/), который будет автоматически запустить службу, если оно еще не запущено.

В следующем фрагменте кода приведен пример того, как запустить привязанную службу в действии с помощью объекта intent явные:

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
> Начиная с Android 5.0 (уровень API 21), его можно только для привязки к службе с явной целью.

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>Архитектурные примечания о соединении и средство привязки.

Некоторые приверженцы ООП может отклонить предыдущей реализации `TimestampBinder` класса, так как это нарушение [закон Деметры](https://en.wikipedia.org/wiki/Law_of_Demeter) , в простейшей форме указывающему «не общаться незнакомцам; только общаться с друзьями». Эта конкретная реализация предоставляет конкретных `TimestampService` класс ко всем клиентам.

Строго говоря, это необязательно для клиента, чтобы знать о `TimestampService` и предоставление этого конкретного класса, чтобы клиенты могут сделать приложение более нестабильным и еще сложнее обслуживать в течение времени его существования. Альтернативный подход — использовать интерфейс, который предоставляет `GetFormattedTimestamp()` метод и прокси-сервер вызовы к службе через `Binder` (или классу подключения службы):  

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

Данном конкретном примере позволяют действию для вызова методов для самой службы:

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
