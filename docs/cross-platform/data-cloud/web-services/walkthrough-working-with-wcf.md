---
title: Пошаговое руководство. Работа с WCF
description: В этом пошаговом руководстве описывается, как можно использовать веб-службы WCF, с помощью класса BasicHttpBinding мобильных приложений, созданных с помощью Xamarin.
ms.prod: xamarin
ms.assetid: D0E83342-2E79-4D25-BD57-43718F5628C4
author: asb3993
ms.author: amburns
ms.date: 02/17/2018
ms.openlocfilehash: b0502028985cf35498a5811ec88b9e32d74d0311
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33918868"
---
# <a name="walkthrough---working-with-wcf"></a>Пошаговое руководство. Работа с WCF

_В этом пошаговом руководстве описывается, как можно использовать веб-службы WCF, с помощью класса BasicHttpBinding мобильных приложений, созданных с помощью Xamarin._


Он является общим требованием для мобильных приложений иметь возможность обмениваться данными с системами серверной части. Существует множество вариантов и параметры для серверной платформы, один из которых является [Windows Communication Foundation](http://msdn.microsoft.com/library/ms731082.aspx) (WCF). В этом пошаговом руководстве будет предоставлять примером как Xamarin мобильного приложения можно использовать службы WCF с помощью `BasicHttpBinding` класса. Пошаговое руководство содержит следующие подразделы:

1.  **Создание службы WCF** -в этом разделе мы создадим очень простой службы WCF, имеющий два метода. Первый метод принимает строковый параметр, а в C# объект требует другой метод. В этом разделе также описывается, как Настройка рабочей станции разработчика, чтобы разрешить удаленный доступ к службе WCF.
1.  **Создание приложения Xamarin.Android** -после создания службы WCF, мы создадим простое приложение Xamarin.Android, которое будет использовать службу WCF. В этом разделе рассматривается создание прокси-класс службы WCF для взаимодействия со службой WCF.
1.  **Создание приложения Xamarin.iOS** -последней части этого учебника предполагает создание простого приложения Xamarin.iOS, который будет использовать службу WCF.

<a name="Requirements" />

## <a name="requirements"></a>Требования

В этом пошаговом руководстве предполагается, что некоторые навыки работы с созданием и использованием службы WCF.

<a name="Creating_a_WCF_Service" />

## <a name="creating-a-wcf-service"></a>Создание службы WCF

Первая задача перед нам является создание службы WCF для мобильных приложений для обмена данными с.

1. Запустите Visual Studio 2017 г. и создайте новый проект.
1. В **новый проект** диалогового окна выберите **WCF > Библиотека службы WCF** шаблон и имя решения `HelloWorldService`:

    ![](walkthrough-working-with-wcf-images/new-wcf-service.png "Создание новой библиотеки службы WCF")

1. В **обозревателе решений**, добавьте новый класс с именем `HelloWorldData` в проект:

    ```csharp
        using System.Runtime.Serialization;

        namespace HelloWorldService
        {
            [DataContract]
            public class HelloWorldData
            {
                [DataMember]
                public bool SayHello { get; set; }

                [DataMember]
                public string Name { get; set; }

                public HelloWorldData()
                {
                    Name = "Hello ";
                    SayHello = false;
                }
            }
        }
    ```


1. В **обозревателе решений**, переименуйте `IService1.cs` для `IHelloWorldService.cs`и переименуйте `Service1.cs` для `HelloWorldService.cs`.
1. В **обозревателе решений**откройте `IHelloWorldService.cs` и замените код следующим кодом:

    ```csharp
        using System.ServiceModel;

        namespace HelloWorldService
        {
            [ServiceContract]
            public interface IHelloWorldService
            {
                [OperationContract]
                string SayHelloTo(string name);

                [OperationContract]
                HelloWorldData GetHelloData(HelloWorldData helloWorldData);
            }
        }
    ```
  
    Эта служба предоставляет два метода — одна из которых принимает строковое значение для параметра и другой, принимает объект .NET.

1. В **обозревателе решений**откройте `HelloWorldService.cs` и замените код следующим кодом:

    ```csharp
        using System;

        namespace HelloWorldService
        {
            public class HelloWorldService : IHelloWorldService
            {
                public HelloWorldData GetHelloData(HelloWorldData helloWorldData)
                {
                    if (helloWorldData == null)
                        throw new ArgumentException("helloWorldData");

                    if (helloWorldData.SayHello)
                        helloWorldData.Name = "Hello World to {helloWorldData.Name}";

                    return helloWorldData;
                }

                public string SayHelloTo(string name)
                {
                    return "Hello World to you, {name}";
                }
            }
        }
    ```

1. В **обозревателе решений**откройте `App.config`, обновить `name` атрибут `<service>` узел, `contract` атрибут `<endpoint>` узел и `baseAddress` атрибут `<add>` узла:

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            ...
            <services>
              <service name="HelloWorldService.HelloWorldService">
                <endpoint address="" binding="basicHttpBinding" contract="HelloWorldService.IHelloWorldService">
                  <identity>
                    <dns value="localhost" />
                  </identity>
                </endpoint>
                <endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" />
                <host>
                  <baseAddresses>
                    <add baseAddress="http://localhost:8733/Design_Time_Addresses/HelloWorldService/" />
                  </baseAddresses>
                </host>
              </service>
            </services>
            ...
        </configuration>
    ```

1. Постройте и запустите службу WCF. Служба будет размещено в тестовом клиенте WCF:

    ![](walkthrough-working-with-wcf-images/hosted-wcf-service.png "В тестовом клиенте службы WCF")

1. Тестовый клиент WCF запущена запустите обозреватель и переходить к конечной точки для службы WCF:

    ![](walkthrough-working-with-wcf-images/wcf-service-browser.png "Страница сведений для браузера службы WCF")

> [!IMPORTANT]
> Следующий раздел необходим только в том случае, если требуется принимать удаленные подключения на рабочей станции Windows 10. Раздел можно игнорировать, если у вас есть альтернативной платформе, на которых требуется развернуть службу WCF.

<a name="Allow_Remote_Access_to_IIS_Express" />

## <a name="configuring-remote-access-to-iis-express"></a>Настройка удаленного доступа для IIS Express

Размещение WCF локально подходит в том случае, если подключения приходят только на локальном компьютере. Тем не менее удаленные устройства (например, устройство Android или iPhone) не будет доступа к локальной службе WCF. Таким образом в этом разделе описывается настройка Windows 10 и IIS Express прием удаленных соединений:

1.  **Настройте сервер IIS Express для принятия удаленных подключений** -это включает редактирование файла конфигурации для IIS Express прием удаленных соединений через определенный порт с последующей настройкой правила для IIS Express для приема входящего трафика.
1.  **Добавить исключение брандмауэра Windows** -необходимо открыть порт в брандмауэре Windows, удаленные приложения можно использовать для взаимодействия со службой WCF.

    Будет необходимо знать IP-адрес рабочей станции. Для этого примера мы предположим, что наши рабочей станции имеет IP-адрес 192.168.1.143.

1. Рассмотрим настройки IIS Express для прослушивания внешних запросов. Это можно сделать путем изменения файла конфигурации для IIS Express в `[solutiondirectory]\.vs\config\applicationhost.config`, как показано на следующем снимке экрана:

    [![](walkthrough-working-with-wcf-images/image05.png "Мы это можно сделать путем изменения файла конфигурации для IIS Express в solutiondirectory.vsconfigapplicationhost.config, как показано на этом снимке экрана")](walkthrough-working-with-wcf-images/image05.png#lightbox)

    Найдите `site` элемент с именем `HelloWorldWcfHost`. Он должен выглядеть примерно в следующем фрагменте XML:

    ```xml
        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
            </bindings>
        </site>
    ```
 
    Вам потребуется добавить другой `binding` открыть порт 8734 для внешнего трафика. Добавьте следующий XML-код для `bindings` элемент, заменив IP-адреса IP-адрес:

    ```xml
    <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />
    ```
    
    Это настроит сервер IIS Express для приема трафика HTTP из любой удаленный IP-адрес, порт 8734 внешний IP-адрес компьютера. В предыдущем фрагменте кода предполагается, что IP-адрес компьютера под управлением IIS Express является 192.168.1.143. После внесения изменений `bindings` элемент должен выглядеть следующим образом:

    ```xml
        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
                <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />
            </bindings>
        </site>
    ```

1. Затем нужно настроить IIS Express прием входящих подключений через порт 8734. Загрузка копии командную строку администратора и выполните следующую команду:

    `> netsh http add urlacl url=http://192.168.1.143:9608/ user=everyone`

1. Последний шаг — Настройка брандмауэра Windows для обеспечения внешнего трафика через порт 8734. Из административной командной строки выполните следующую команду:

    `> netsh advfirewall firewall add rule name="IISExpressXamarin" dir=in protocol=tcp localport=8734 profile=private remoteip=localsubnet action=allow`

    Эта команда позволит входящий трафик на порте 8734 всех устройств в одной подсети как рабочая станция Windows 10.

Вы создали очень простой службы WCF, размещенных в IIS Express, которая будет принимать входящие подключения от других устройств или компьютеров в нашем подсети. Это можно проверить, запуск приложения и посещение `http://localhost:8733/Design_Time_Addresses/HelloWorldService/` на рабочей станции и `http://192.168.1.143:8734/Design_Time_Addresses/HelloWorldService/` с другого компьютера в подсети.

Чтобы сервер IIS Express для обеспечения выполнения и обслуживающая службу, выключите **изменить и продолжить** в диалоговом окне *свойства проекта > Web > отладчики*.

## <a name="creating-a-proxy-for-the-web-service"></a>Создание учетной записи-посредника для веб-службы

Перед приложения могут использовать службу, необходимо создать прокси веб-службы для службы WCF. Это можно обеспечить, выполнив следующие действия.

1. Добавить .NET стандартной библиотеки классов с именем `HelloWorldServiceProxy`и удалите все классы в проекте.
1. Запустите `HelloWorldService` проекта.
1. С `HelloWorldService` работает проект, добавить новый **подключенной службы** в проект с помощью **поставщиком услуг ссылку WCF Microsoft**.
1. В **конечной точки службы** вкладке **настроить ссылку веб-службы WCF** диалоговое окно, нажмите кнопку **Discover** кнопку, удалите `mex` из конца обнаруженных Конечная точка в **URI** раскрывающегося списка, введите `HelloWorldServiceProxy` как **имен**и нажмите кнопку **Далее** кнопки.
1. В **параметры типа данных** вкладке **настроить ссылку веб-службы WCF** диалогового окна, примите значения по умолчанию, щелкнув **Далее** кнопки.
1. В **параметры клиента** вкладке **настроить ссылку веб-службы WCF** диалогового окна, убедитесь, что **открытый** флажок установлен и нажмите кнопку **Готово**  кнопки.
1. Построение `HelloWorldServiceProxy` проекта.

> [!NOTE]
> Вместо создания прокси-сервера, используя Microsoft WCF Web ссылку поставщик службы в Visual Studio 2017 г. является использование ServiceModel Metadata Utility Tool (svcutil.exe). Дополнительные сведения см. в разделе [ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Creating_a_Xamarin_Android_Application" />

## <a name="creating-a-xamarinandroid-application"></a>Создание приложения Xamarin.Android

Прокси-службы WCF может использоваться в приложениях Xamarin.Android, следующим образом:

1. В Visual Studio, добавьте новый пустой проект Android в решение и назовите его `HelloWorld.Android`.
1. В `HelloWorld.Android` проекта, добавьте ссылку на `HelloWorldServiceProxy` проекта, а также ссылку на `System.ServiceModel` пространства имен.
1. В **обозревателе решений**откройте `Resources/layout/main.axml` и замените существующий XML следующий XML-код:

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                  android:orientation="vertical"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent">
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/sayHelloWorldButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/say_hello_world" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/sayHelloWorldTextView" />
            </LinearLayout>
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/getHelloWorldDataButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/get_hello_world_data" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/getHelloWorldDataTextView" />
            </LinearLayout>
        </LinearLayout>
    ```
    
    Следующих снимках экрана показан пользовательский Интерфейс в конструкторе:

    [![](walkthrough-working-with-wcf-images/image09.png "Снимок экрана того, как выглядит этот пользовательский Интерфейс в конструкторе")](walkthrough-working-with-wcf-images/image09.png#lightbox)
    
1. В **обозревателе решений**откройте `Resources/values/Strings.xml` и добавьте следующий код XML:

    ```xml
    <string name="say_hello_world">Say Hello World</string>
    <string name="get_hello_world_data">Get Hello World data</string>
    ```
    
1. В **обозревателе решений**откройте `MainActivity.cs` и замените существующий код следующим кодом:

    ```csharp
        [Activity(Label = "HelloWorld.Android", MainLauncher = true)]
        public class MainActivity : Activity
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");

            HelloWorldServiceClient _client;
            Button _getHelloWorldDataButton;
            TextView _getHelloWorldDataTextView;
            Button _sayHelloWorldButton;
            TextView _sayHelloWorldTextView;
            ...
        }
    ```

    Замените `<insert_WCF_service_endpoint_here>` с адресом конечной точки WCF.

1. В `MainActivity.cs`, изменить `OnCreate` метода, так что он содержит следующий код:

    ```csharp
        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            InitializeHelloWorldServiceClient();

            // This button will invoke the GetHelloWorldData - the method that takes a C# object as a parameter.
            _getHelloWorldDataButton = FindViewById<Button>(Resource.Id.getHelloWorldDataButton);
            _getHelloWorldDataButton.Click += GetHelloWorldDataButtonOnClick;
            _getHelloWorldDataTextView = FindViewById<TextView>(Resource.Id.getHelloWorldDataTextView);

            // This button will invoke SayHelloWorld - this method takes a simple string as a parameter.
            _sayHelloWorldButton = FindViewById<Button>(Resource.Id.sayHelloWorldButton);
            _sayHelloWorldButton.Click += SayHelloWorldButtonOnClick;
            _sayHelloWorldTextView = FindViewById<TextView>(Resource.Id.sayHelloWorldTextView);
        }
    ```
    
    Приведенный выше код инициализирует переменные экземпляра для класса и настраивающий некоторые обработчики событий.

1. В `MainActivity.cs`, создать экземпляр прокси-класса клиента путем добавления следующих двух способов:

    ```csharp
        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }
    ```
    
    Приведенный выше код создает и инициализирует `HelloWorldServiceClient` объекта.

1. В `MainActivity.cs`, добавьте две кнопки в даже обработчики `Activity`:

    ```csharp
        async void GetHelloWorldDataButtonOnClick(object sender, EventArgs e)
        {
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            _getHelloWorldDataTextView.Text = "Waiting for WCF...";
            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                _getHelloWorldDataTextView.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButtonOnClick(object sender, EventArgs e)
        {
            _sayHelloWorldTextView.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                _sayHelloWorldTextView.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    ```
  
1. Запустите приложение, убедитесь, что служба WCF работает и щелкнуть две кнопки. В приложении вызывается WCF асинхронно, при условии, что `Endpoint` правильно набор полей:

    [![](walkthrough-working-with-wcf-images/image08.png "В течение 30 секунд должен быть получен ответ от каждого метода WCF и нашего приложения должна выглядеть примерно так этот снимок экрана")](walkthrough-working-with-wcf-images/image08.png#lightbox)

<a name="Creating_a_Xamarin_iOS_Application" />

## <a name="creating-a-xamarinios-application"></a>Создание приложения Xamarin.iOS

Прокси-службы WCF может использоваться в приложениях Xamarin.iOS, следующим образом:

1. В Visual Studio, добавьте новый iPhone **одним приложением представление** проект в решение и назовите его `HelloWorld.iOS`.
1. В `HelloWorld.iOS` проекта, добавьте ссылку на `HelloWorldServiceProxy` проекта, а также ссылку на `System.ServiceModel` пространства имен.
1. В **обозревателе решений**, дважды щелкните `Main.storyboard` открыть его в конструкторе операций ввода-вывода. Затем добавьте следующие `UIButton` и `UITextView` элементов управления:

    ||name|Заголовок|
    |--- |--- |--- |
    |`UIButton`|`sayHelloWorldButton`|Предположим, «Hello, World»|
    |`UITextView`|`sayHelloWorldText`||
    |`UIButton`|`getHelloWorldDataButton`|«Hello, World» получение данных|
    |`UITextView`|`getHelloWorldDataText`||

    После добавления элементов управления, пользовательский Интерфейс должен выглядеть следующий снимок экрана:

    [![](walkthrough-working-with-wcf-images/image12.png "После добавления элементов управления, пользовательский Интерфейс должен быть похож на этом снимке экрана")](walkthrough-working-with-wcf-images/image12.png#lightbox)

1. В **обозревателе решений**откройте `ViewController.cs` и добавьте следующий код:

    ```xml
        public partial class ViewController : UIViewController
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");
            HelloWorldServiceClient _client;
            ...
        }
    ```
  
    Замените `<insert_WCF_service_endpoint_here>` с адресом конечной точки WCF.

1. В `ViewController.cs`, обновить `ViewDidLoad` метода, так что он имеет следующий вид:

    ```csharp
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();
            InitializeHelloWorldServiceClient();

            getHelloWorldDataButton.TouchUpInside += GetHelloWorldDataButton_TouchUpInside;
            sayHelloWorldButton.TouchUpInside += SayHelloWorldButton_TouchUpInside;
        }
    ```
  
1. В `ViewController.cs`, добавьте `InitializeHelloWorldServiceClient` и `CreateBasicHttpBinding` методов:

    ```csharp
        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }
    ```
  
1. В `ViewController.cs`, добавить обработчики событий для `TouchUpInside` события по двум `UIButton` экземпляров:

    ```csharp
        async void GetHelloWorldDataButton_TouchUpInside(object sender, EventArgs e)
        {
            getHelloWorldDataText.Text = "Waiting for WCF...";
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                getHelloWorldDataText.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButton_TouchUpInside(object sender, EventArgs e)
        {
            sayHelloWorldText.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                sayHelloWorldText.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    ```

1. Запустите приложение, убедитесь, что служба WCF работает и щелкнуть две кнопки. В приложении вызывается WCF асинхронно, при условии, что `Endpoint` правильно набор полей:

    [![](walkthrough-working-with-wcf-images/image10.png "В течение 30 секунд должен быть получен ответ от каждого метода WCF и наше приложение должно выглядеть, как на этом снимке экрана")](walkthrough-working-with-wcf-images/image10.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Сводка

Этот учебник рассмотрены способы работы со службой WCF с помощью Xamarin.Android и Xamarin.iOS мобильного приложения. Оно было показано, как создать службу WCF и описаны способы настройки Windows 10 и IIS Express, чтобы принимать подключения от удаленных устройств. Затем описано, как создать прокси клиента WCF и было показано, как использовать прокси-клиента в приложениях, Xamarin.Android и Xamarin.iOS.


## <a name="related-links"></a>Связанные ссылки

- [HelloWorld (пример)](https://developer.xamarin.com/samples/mobile/WCF-Walkthrough/)
- [Разработка сервисноориентированных приложений с помощью WCF](https://docs.microsoft.com/dotnet/framework/wcf/index)
- [Как: создание клиента Windows Communication Foundation](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Средство ServiceModel Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
