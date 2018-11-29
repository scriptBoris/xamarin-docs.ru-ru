---
title: CallKit в Xamarin.iOS
description: В этой статье рассматриваются новые API CallKit, Apple, выпущенная в iOS 10 и способы его реализации в приложениях Xamarin.iOS VOIP.
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 40d4a6cbd3bb8e3bd4c55c50c69f85f91d94feac
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459932"
---
# <a name="callkit-in-xamarinios"></a>CallKit в Xamarin.iOS

Новый интерфейс API CallKit в iOS 10 предоставляет способ для приложений VOIP для интеграции с iPhone пользовательского интерфейса и обеспечивают привычный интерфейс и возможности для конечного пользователя. С помощью этого API пользователи могут просматривать и взаимодействовать с вызовами VOIP с экрана блокировки устройства iOS и управлять контактами с помощью приложения Phone **"Избранное"** и **последнее** представления.

## <a name="about-callkit"></a>О CallKit

В соответствии с Apple CallKit — это новая платформа, которая повысит сторонние приложения голоса по IP (VOIP) самого лицам принцип работы в iOS 10. CallKit API позволяет приложениям VOIP для интеграции с iPhone пользовательского интерфейса и обеспечивают привычный интерфейс и возможности для конечного пользователя. Так же, как встроенное приложение телефона, пользователь может просматривать и взаимодействовать с вызовами VOIP с экрана блокировки устройства iOS и управлять контактами с помощью приложения Phone **"Избранное"** и **последнее** представления.

Кроме того CallKit API предоставляет возможность создавать расширения приложений, которое может связать телефонный номер с именем (Звонящего) или сообщить, что системы, когда число должно быть заблокирован (вызвать блокировки).

### <a name="the-existing-voip-app-experience"></a>Существующие взаимодействие VOIP с приложениями

Прежде чем обсуждать новые API CallKit и его возможностей, взглянуть на работу текущего пользователя с сторонней сторонних приложений VOIP в iOS 9 (и меньшей) с использованием вымышленной VOIP приложение, которое называется MonkeyCall. MonkeyCall — это простое приложение, которое позволяет пользователю отправлять и получать вызовов VOIP, с помощью существующих API iOS.

В настоящее время если пользователь получает входящего вызова на MonkeyCall и их iPhone заблокирован, уведомления, полученного на экране блокировки невозможно отличить от других видов уведомлений (таких как типы из сообщения или почтовые приложения, например).

Если пользователю было ответить на вызов, их придется слайд MonkeyCall уведомление, чтобы открыть приложение и введите их секретного кода (или пользователь Touch ID) чтобы разблокировать устройство, прежде чем они могут принимать вызовы и начать диалог.

Интерфейс очень столь же неудобно, если телефон разблокирован. Опять же входящий вызов MonkeyCall отображается как стандартный баннер, слайды в верхней части экрана. Так как уведомление является временным, его можно легко пропустить пользователем, заставляя их открыть центр уведомлений и найти конкретного уведомления, чтобы ответить на, а затем вызовите или найти и вручную запустить приложение MonkeyCall.

### <a name="the-callkit-voip-app-experience"></a>Взаимодействие с приложениями CallKit VOIP

Путем реализации новых интерфейсов API CallKit в приложении MonkeyCall, удобство работы пользователя с входящего вызова VOIP может сильно повысить в iOS 10. Рассмотрим в качестве примера получение вызова VOIP, при блокировке выше свой телефон пользователя. Реализовав CallKit, вызов будет отображаться на экране блокировки iPhone, как и в случае, если вызов поступил из встроенные приложения для телефонов, весь экран, собственного пользовательского интерфейса и стандартных функциональных возможностей проведите по экрану ответов.

Опять же если iPhone будет разблокирован, при получении вызова MonkeyCall VOIP, же весь экран, собственного пользовательского интерфейса и стандартных возможностей проведите по экрану ответов и tap отклонить встроенные приложения представляется телефона и MonkeyCall может воспроизводить пользовательских мелодию звонка .

CallKit предоставляет функциональные возможности MonkeyCall, позволяя VOIP его вызывает для взаимодействия с другими типами вызовов в встроенных недавних объектов и список избранного, чтобы использовать встроенные функции не беспокоить и блок, запустите MonkeyCall вызовы в Siri и позволяет пользователям назначать вызовы MonkeyCall людей в приложении "Контакты".

В следующих разделах будут описаны архитектура CallKit, входящих и исходящих вызовов потоков и CallKit API подробно.

## <a name="the-callkit-architecture"></a>Архитектура CallKit

В iOS 10 Apple ввела CallKit во всех системных служб, таким образом, что вызовы, выполняемые для CarPlay, например, известно, что пользовательский Интерфейс системы через CallKit. В примере, приведенные ниже, поскольку MonkeyCall принимает CallKit, он известен в систему в так же, как эти встроенные системные службы и получает все те же функции:

[![](callkit-images/callkit01.png "Стек CallKit службы")](callkit-images/callkit01.png#lightbox)

Рассмотрим более подробно в приложение MonkeyCall из приведенной выше схеме. Приложение содержит весь код для взаимодействия с собственной сети и содержит свои собственные пользовательские интерфейсы. Он связывает в CallKit для взаимодействия с системой:

[![](callkit-images/callkit02.png "Архитектура приложения MonkeyCall")](callkit-images/callkit02.png#lightbox)

Существует два основных интерфейса в CallKit, используемую приложением:

- `CXProvider` -Это позволяет приложению MonkeyCall для информирования системы-резервному уведомления, которые могут возникнуть.
- `CXCallController` -Позволяет приложению MonkeyCall для информирования системы действий локального пользователя.

### <a name="the-cxprovider"></a>CXProvider

Как уже говорилось, `CXProvider` позволяет приложению для информирования системы-резервному уведомления, которые могут возникнуть. Это уведомление, которое не происходит из-за действий локального пользователя, но возникают из-за внешних событий, таких как входящие вызовы.

Приложение должно использовать `CXProvider` в следующих целях:

- Отчет входящего вызова в системе.
- О том, что исходящий вызов был подключен к системе.
- Отчет Завершение вызова в системе удаленного пользователя.

Когда приложение пытается установить соединение в систему, он использует `CXCallUpdate` класса и когда система должна взаимодействовать с приложением, он использует `CXAction` класса:

[![](callkit-images/callkit03.png "Взаимодействие с системой через CXProvider")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>CXCallController

`CXCallController` Позволяет приложению для информирования системы действия локального пользователя, такие как пользователей, запускающих звонок по протоколу VOIP. Путем реализации `CXCallController` приложение получает осложняется с другими типами вызовов в системе. Например, если уже имеется вызов active телефонии в состоянии выполнения `CXCallController` можно разрешить приложению VOIP поместить этот вызов на удержании и начать или ответить на звонок по протоколу VOIP.

Приложение должно использовать `CXCallController` в следующих целях:

- Сообщают, когда пользователь начинает исходящий вызов в систему.
- Отчет, когда пользователь отвечает на входящий звонок в систему.
- Отчет, когда пользователь завершает вызов в систему.

Когда приложение пытается установить соединение действия локального пользователя в систему, он использует `CXTransaction` класса:

[![](callkit-images/callkit04.png "Отчеты в систему с помощью CXCallController")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>Реализация CallKit

Ниже будет показано, как реализовать CallKit в приложении Xamarin.iOS VOIP. Для примера этот документ будет использовать код из вымышленного приложения MonkeyCall VOIP. Приведенный здесь код представляет несколько вспомогательных классов, CallKit, определенных частей будут подробно рассмотрены в следующих разделах.

### <a name="the-activecall-class"></a>Класс ActiveCall

`ActiveCall` Приложением MonkeyCall класс используется для хранения всех данных о вызове VOIP, который сейчас активен, следующим образом:

```csharp
using System;
using CoreFoundation;
using Foundation;

namespace MonkeyCall
{
    public class ActiveCall
    {
        #region Private Variables
        private bool isConnecting;
        private bool isConnected;
        private bool isOnhold;
        #endregion

        #region Computed Properties
        public NSUuid UUID { get; set; }
        public bool isOutgoing { get; set; }
        public string Handle { get; set; }
        public DateTime StartedConnectingOn { get; set;}
        public DateTime ConnectedOn { get; set;}
        public DateTime EndedOn { get; set; }

        public bool IsConnecting {
            get { return isConnecting; }
            set {
                isConnecting = value;
                if (isConnecting) StartedConnectingOn = DateTime.Now;
                RaiseStartingConnectionChanged ();
            }
        }

        public bool IsConnected {
            get { return isConnected; }
            set {
                isConnected = value;
                if (isConnected) {
                    ConnectedOn = DateTime.Now;
                } else {
                    EndedOn = DateTime.Now;
                }
                RaiseConnectedChanged ();
            }
        }

        public bool IsOnHold {
            get { return isOnhold; }
            set {
                isOnhold = value;
            }
        }
        #endregion

        #region Constructors
        public ActiveCall ()
        {
        }

        public ActiveCall (NSUuid uuid, string handle, bool outgoing)
        {
            // Initialize
            this.UUID = uuid;
            this.Handle = handle;
            this.isOutgoing = outgoing;
        }
        #endregion

        #region Public Methods
        public void StartCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call starting successfully
            completionHandler (true);

            // Simulate making a starting and completing a connection
            DispatchQueue.MainQueue.DispatchAfter (new DispatchTime(DispatchTime.Now, 3000), () => {
                // Note that the call is starting
                IsConnecting = true;

                // Simulate pause before connecting
                DispatchQueue.MainQueue.DispatchAfter (new DispatchTime (DispatchTime.Now, 1500), () => {
                    // Note that the call has connected
                    IsConnecting = false;
                    IsConnected = true;
                });
            });
        }

        public void AnswerCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call being answered
            IsConnected = true;
            completionHandler (true);
        }

        public void EndCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call ending
            IsConnected = false;
            completionHandler (true);
        }
        #endregion

        #region Events
        public delegate void ActiveCallbackDelegate (bool successful);
        public delegate void ActiveCallStateChangedDelegate (ActiveCall call);

        public event ActiveCallStateChangedDelegate StartingConnectionChanged;
        internal void RaiseStartingConnectionChanged ()
        {
            if (this.StartingConnectionChanged != null) this.StartingConnectionChanged (this);
        }

        public event ActiveCallStateChangedDelegate ConnectedChanged;
        internal void RaiseConnectedChanged ()
        {
            if (this.ConnectedChanged != null) this.ConnectedChanged (this);
        }
        #endregion
    }
}
```

`ActiveCall` содержит несколько свойств, описывающих состояние вызова и два события, которые могут возникать при изменении состояния вызова. Так как это только для примера, существует три метода, используемый для моделирования, ответа от вызова.

### <a name="the-startcallrequest-class"></a>Класс StartCallRequest

`StartCallRequest` Статический класс содержит несколько вспомогательных методов, которые будут использоваться при работе с исходящие вызовы:

```csharp
using System;
using Foundation;
using Intents;

namespace MonkeyCall
{
    public static class StartCallRequest
    {
        public static string URLScheme {
            get { return "monkeycall"; }
        }

        public static string ActivityType {
            get { return INIntentIdentifier.StartAudioCall.GetConstant ().ToString (); }
        }

        public static string CallHandleFromURL (NSUrl url)
        {
            // Is this a MonkeyCall handle?
            if (url.Scheme == URLScheme) {
                // Yes, return host
                return url.Host;
            } else {
                // Not handled
                return null;
            }
        }

        public static string CallHandleFromActivity (NSUserActivity activity)
        {
            // Is this a start call activity?
            if (activity.ActivityType == ActivityType) {
                // Yes, trap any errors
                try {
                    // Get first contact
                    var interaction = activity.GetInteraction ();
                    var startAudioCallIntent = interaction.Intent as INStartAudioCallIntent;
                    var contact = startAudioCallIntent.Contacts [0];

                    // Get the person handle
                    return contact.PersonHandle.Value;
                } catch {
                    // Error, report null
                    return null;
                }
            } else {
                // Not handled
                return null;
            }
        }
    }
}
```

`CallHandleFromURL` И `CallHandleFromActivity` классы используются в AppDelegate получить дескриптор контактные лица, вызываемой в исходящий вызов. Дополнительные сведения см. в разделе [обработки исходящие вызовы](#Handling-Outgoing-Calls) разделе ниже.

### <a name="the-activecallmanager-class"></a>Класс ActiveCallManager

`ActiveCallManager` Класс обрабатывает все открытые вызовы в приложении MonkeyCall.

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using CallKit;

namespace MonkeyCall
{
    public class ActiveCallManager
    {
        #region Private Variables
        private CXCallController CallController = new CXCallController ();
        #endregion

        #region Computed Properties
        public List<ActiveCall> Calls { get; set; }
        #endregion

        #region Constructors
        public ActiveCallManager ()
        {
            // Initialize
            this.Calls = new List<ActiveCall> ();
        }
        #endregion

        #region Private Methods
        private void SendTransactionRequest (CXTransaction transaction)
        {
            // Send request to call controller
            CallController.RequestTransaction (transaction, (error) => {
                // Was there an error?
                if (error == null) {
                    // No, report success
                    Console.WriteLine ("Transaction request sent successfully.");
                } else {
                    // Yes, report error
                    Console.WriteLine ("Error requesting transaction: {0}", error);
                }
            });
        }
        #endregion

        #region Public Methods
        public ActiveCall FindCall (NSUuid uuid)
        {
            // Scan for requested call
            foreach (ActiveCall call in Calls) {
                if (call.UUID.Equals(uuid)) return call;
            }

            // Not found
            return null;
        }

        public void StartCall (string contact)
        {
            // Build call action
            var handle = new CXHandle (CXHandleType.Generic, contact);
            var startCallAction = new CXStartCallAction (new NSUuid (), handle);

            // Create transaction
            var transaction = new CXTransaction (startCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void EndCall (ActiveCall call)
        {
            // Build action
            var endCallAction = new CXEndCallAction (call.UUID);

            // Create transaction
            var transaction = new CXTransaction (endCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void PlaceCallOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, true);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void RemoveCallFromOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, false);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }
        #endregion
    }
}
```

Еще раз, так как это только, моделирование `ActiveCallManager` только поддерживает коллекцию `ActiveCall` объектов и имеет подпрограммы для поиска определенного вызова с его `UUID` свойство. Он также включает методы для запуска, завершения и изменить состояние на время удержания звонка исходящий вызов. Дополнительные сведения см. в разделе [обработки исходящие вызовы](#Handling-Outgoing-Calls) разделе ниже.

### <a name="the-providerdelegate-class"></a>Класс ProviderDelegate

Как уже говорилось ранее, `CXProvider` предоставляет двусторонний обмен данными между приложением и системе для уведомления каналу. Разработчику необходимо предоставить пользовательский `CXProviderDelegate` и присоединить его к `CXProvider` для приложения для обработки событий CallKit-каналу. MonkeyCall использует следующие `CXProviderDelegate`:

```csharp
using System;
using Foundation;
using CallKit;
using UIKit;

namespace MonkeyCall
{
    public class ProviderDelegate : CXProviderDelegate
    {
        #region Computed Properties
        public ActiveCallManager CallManager { get; set;}
        public CXProviderConfiguration Configuration { get; set; }
        public CXProvider Provider { get; set; }
        #endregion

        #region Constructors
        public ProviderDelegate (ActiveCallManager callManager)
        {
            // Save connection to call manager
            CallManager = callManager;

            // Define handle types
            var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };

            // Get Image Template
            var templateImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconTemplateImageData = templateImage.AsPNG(),
                RingtoneSound = "musicloop01.wav"
            };

            // Create a new provider
            Provider = new CXProvider (Configuration);

            // Attach this delegate
            Provider.SetDelegate (this, null);

        }
        #endregion

        #region Override Methods
        public override void DidReset (CXProvider provider)
        {
            // Remove all calls
            CallManager.Calls.Clear ();
        }

        public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
        {
            // Create new call record
            var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

            // Monitor state changes
            activeCall.StartingConnectionChanged += (call) => {
                if (call.isConnecting) {
                    // Inform system that the call is starting
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
                }
            };

            // Start call
            activeCall.StartCall ((successful) => {
                // Was the call able to be started?
                if (successful) {
                    // Yes, inform the system
                    action.Fulfill ();

                    // Add call to manager
                    CallManager.Calls.Add (activeCall);
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.AnswerCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.EndCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Remove call from manager's queue
                    CallManager.Calls.Remove (call);

                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformSetHeldCallAction (CXProvider provider, CXSetHeldCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Update hold status
            call.isOnHold = action.OnHold;

            // Inform system of success
            action.Fulfill ();
        }

        public override void TimedOutPerformingAction (CXProvider provider, CXAction action)
        {
            // Inform user that the action has timed out
        }

        public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // Start the calls audio session here
        }

        public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // End the calls audio session and restart any non-call
            // related audio
        }
        #endregion

        #region Public Methods
        public void ReportIncomingCall (NSUuid uuid, string handle)
        {
            // Create update to describe the incoming call and caller
            var update = new CXCallUpdate ();
            update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

            // Report incoming call to system
            Provider.ReportNewIncomingCall (uuid, update, (error) => {
                // Was the call accepted
                if (error == null) {
                    // Yes, report to call manager
                    CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
                } else {
                    // Report error to user here
                    Console.WriteLine ("Error: {0}", error);
                }
            });
        }
        #endregion
    }
}
```

При создании экземпляра делегата он передается `ActiveCallManager` , он будет использоваться для обработки любого вызова действия. Затем он определяет типы дескрипторов (`CXHandleType`), `CXProvider` будет отвечать на:

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

И он получает образ шаблона, которая будет применяться для значка приложения, когда вызов выполняется:

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

Эти значения получить объединяется в `CXProviderConfiguration` , будет использоваться для настройки `CXProvider`:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

Делегат затем создает новый `CXProvider` с такой конфигурацией и подключающегося к нему:

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

При использовании CallKit, приложение больше не создаст и обрабатывать свои аудио сеансы, вместо этого его необходимо настроить и использовать сеанс аудио, который система создания и обработки для него. 

Если бы это было настоящее приложение, `DidActivateAudioSession` метод будет использоваться для запуска вызова с помощью предварительно настроенных `AVAudioSession` , предоставленный системе:

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

Она также будет использовать `DidDeactivateAudioSession` аудио сеанса указан метод finalize и освободить его подключение к системе:

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

В последующих разделах подробно рассматриваются остальной части кода.

### <a name="the-appdelegate-class"></a>Класс AppDelegate

Для хранения экземпляров MonkeyCall используется AppDelegate `ActiveCallManager` и `CXProviderDelegate` , будет использоваться в приложении:

```csharp
using Foundation;
using UIKit;
using Intents;
using System;

namespace MonkeyCall
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        #region Constructors
        public override UIWindow Window { get; set; }
        public ActiveCallManager CallManager { get; set; }
        public ProviderDelegate CallProviderDelegate { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Initialize the call handlers
            CallManager = new ActiveCallManager ();
            CallProviderDelegate = new ProviderDelegate (CallManager);

            return true;
        }

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            // Get handle from url
            var handle = StartCallRequest.CallHandleFromURL (url);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from URL: {0}", url); 
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
        {
            var handle = StartCallRequest.CallHandleFromActivity (userActivity);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        ...
        #endregion
    }
}
```

`OpenUrl` И `ContinueUserActivity` переопределение методов используются в том случае, когда приложение обрабатывает исходящий вызов. Дополнительные сведения см. в разделе [обработки исходящие вызовы](#Handling-Outgoing-Calls) разделе ниже.

## <a name="handling-incoming-calls"></a>Обработка входящих вызовов

Существует несколько состояний и процессы, которые входящего вызова VOIP можно выполнить во время типичный рабочий процесс входящего вызова таких как:

- О том, пользователь (и системы) существовании входящего вызова.
- Получение уведомлений при пользователю необходимо ответить на вызов и инициализация вызова с другим пользователем.
- Сообщите системе и сетевого взаимодействия, когда пользователь хочет завершения текущего вызова.

Следующие разделы займет подробно рассматривались как приложение может использовать CallKit для обработки входящих рабочим процессом вызова, снова, используя приложение MonkeyCall VOIP в качестве примера.

### <a name="informing-user-of-incoming-call"></a>О том, что пользователь вызовов

Удаленный пользователь был начат диалог VOIP с локального пользователя, происходит следующее:

[![](callkit-images/callkit05.png "Удаленный пользователь начал диалог VOIP")](callkit-images/callkit05.png#lightbox)

1. Приложение получает уведомление от его сеть обмена данными это входящий вызов VOIP.
2. Приложение использует `CXProvider` для отправки `CXCallUpdate` в систему, сообщая ей вызова.
3. Система публикует вызов пользовательского интерфейса системы, системных служб и любых других приложений VOIP, с помощью CallKit.

Например, в `CXProviderDelegate`:

```csharp
public void ReportIncomingCall (NSUuid uuid, string handle)
{
    // Create update to describe the incoming call and caller
    var update = new CXCallUpdate ();
    update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

    // Report incoming call to system
    Provider.ReportNewIncomingCall (uuid, update, (error) => {
        // Was the call accepted
        if (error == null) {
            // Yes, report to call manager
            CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
        } else {
            // Report error to user here
            Console.WriteLine ("Error: {0}", error);
        }
    });
}
```

Этот код создает новый `CXCallUpdate` экземпляра и присоединяет к нему, которому будет определяться вызывающий объект дескриптора. После этого он выполняет `ReportNewIncomingCall` метод `CXProvider` класса для информирования системы вызова. При успешном выполнении, вызов добавляется к коллекции активных вызовов приложения, в противном случае ошибки необходимо сообщить пользователю.

### <a name="user-answering-incoming-call"></a>Ответы на входящий звонок пользователя

Если пользователю для ответа на входящий звонок по протоколу VOIP, происходит следующее:

[![](callkit-images/callkit06.png "Пользователь отвечает на входящий звонок VOIP")](callkit-images/callkit06.png#lightbox)

1. Пользовательский Интерфейс системы сообщает системе, что пользователю необходимо ответить на звонок по протоколу VOIP.
2. Система отправляет `CXAnswerCallAction` в приложение `CXProvider` сообщая ей способа ответов.
3. Приложение сообщает его сетевого обмена данными, что пользователь отвечает на вызов, и звонок по протоколу VOIP продолжает работу как обычно.

Например, в `CXProviderDelegate`:

```csharp
public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.AnswerCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Этот код сначала выполняет поиск заданного вызова в свой список активных вызовов. Если вызов не найден, система получает уведомление, и метод завершает работу. Если он найден, `AnswerCall` метод `ActiveCall` класса вызывается для запуска вызова и система сведения при его успешном или неуспешном.

### <a name="user-ending-incoming-call"></a>Завершение входящего вызова пользователя

Если пользователь хочет к прерыванию вызова из в пользовательском Интерфейсе приложения, происходит следующее:

[![](callkit-images/callkit07.png "Пользователь завершает вызов из в пользовательском Интерфейсе приложения")](callkit-images/callkit07.png#lightbox)

1. Приложение создает `CXEndCallAction` , получает объединяется в `CXTransaction` , отправляется в систему для информирования что вызов завершается.
2. Система проверяет цель вызова End и отправляет `CXEndCallAction` обратно к приложению через `CXProvider`.
3. Приложение информирует его сетевого взаимодействия, вызов завершается.

Например, в `CXProviderDelegate`:

```csharp
public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.EndCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Remove call from manager's queue
            CallManager.Calls.Remove (call);

            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Этот код сначала выполняет поиск заданного вызова в свой список активных вызовов. Если вызов не найден, система получает уведомление, и метод завершает работу. Если он найден, `EndCall` метод `ActiveCall` вызывается для завершения вызова и система сведения при его успешном или неуспешном. В случае успешного выполнения вызов удаляется из коллекции активных вызовов.

## <a name="managing-multiple-calls"></a>Управление несколькими вызовами

Большинство приложений VOIP может обрабатывать несколько вызовов за один раз. Например, если в настоящее время нет активных звонок по протоколу VOIP и получает уведомление приложения, что имеется новый входящий вызов пользователю можно приостановить или сигнала отбоя при первом вызове, чтобы ответить на второй.

В ситуации предоставить выше, система будет отправлять `CXTransaction` к приложению, которое будет содержать список из нескольких действий (таких как `CXEndCallAction` и `CXAnswerCallAction`). Все эти действия будут должны быть выполнены по отдельности, что система может соответствующим образом обновлять пользовательский Интерфейс.

## <a name="handling-outgoing-calls"></a>Обработка исходящих вызовов

Если пользователь касается запись из списка недавних объектов (в приложении для телефонов), например, это из вызова, относящихся к приложению, он будет отправляться _начать вызывать намерение_ системой:

[![](callkit-images/callkit08.png "Получение начала вызова намерение")](callkit-images/callkit08.png#lightbox)

1. Приложение создаст _действие при запуске вызова_ на основе начать вызывать пожеланий, полученный из системы. 
2. Приложение будет использовать `CXCallController` для запроса вызвать действие при запуске из системы.
3. Если система принимает действие, он будет возвращен в приложение с помощью `XCProvider` делегировать.
4. Приложение запускается исходящий вызов с помощью его сетевого взаимодействия.

Дополнительные сведения о намерения, см. в разделе наших [Intents и Intents UI расширения](~/ios/platform/sirikit/understanding-sirikit.md) документации. 

### <a name="the-outgoing-call-lifecycle"></a>Жизненный цикл исходящих вызовов

При работе с CallKit и исходящий вызов приложения нужно будет сообщить системе из следующих событий жизненного цикла:

1. **Запуск** -информирования системы, исходящий вызов запускается.
2. **К работе** -сообщить системе о запуске исходящий вызов.
3. **Подключение** -информирования системы, к которому подключается исходящий вызов.
4. **Подключение** -информирования исходящий вызов был подключен и что обе стороны могут обратиться теперь.

Например следующий код будет запускать исходящего звонка:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void StartCall (string contact)
{
    // Build call action
    var handle = new CXHandle (CXHandleType.Generic, contact);
    var startCallAction = new CXStartCallAction (new NSUuid (), handle);

    // Create transaction
    var transaction = new CXTransaction (startCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Он создает `CXHandle` и использует ее для настройки `CXStartCallAction` получаемого в `CXTransaction` то есть отправляемые системы с помощью `RequestTransaction` метод `CXCallController` класса. Путем вызова `RequestTransaction` метод системы можно поместить все существующие вызовы на время удержания звонка, независимо от источника (приложения Phone, FaceTime, VOIP, и т.д.), перед запуском нового вызова.

Запрос на запуск исходящего звонка VOIP могут поступать из нескольких источников, например Siri, запись в карточку контакта (в приложении "Контакты") или из списка недавних объектов (в приложении для телефонов). В таких ситуациях приложение будет отправлено начать вызывать намерение внутри `NSUserActivity` и AppDelegate нужно будет обрабатывать его:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var handle = StartCallRequest.CallHandleFromActivity (userActivity);

    // Found?
    if (handle == null) {
        // No, report to system
        Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
        return false;
    } else {
        // Yes, start call and inform system
        CallManager.StartCall (handle);
        return true;
    }
}
```

Здесь `CallHandleFromActivity` метод вспомогательного класса `StartCallRequest` , используется для получения дескриптора пользователю, вызываемой (см. в разделе [класс StartCallRequest](#The-StartCallRequest-Class) выше). 

`PerformStartCallAction` Метод [ProviderDelegate класс](#The-ProviderDelegate-Class) используется наконец запустить фактическое исходящий вызов и информирования системы его жизненного цикла:

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
        }
    };

    // Start call
    activeCall.StartCall ((successful) => {
        // Was the call able to be started?
        if (successful) {
            // Yes, inform the system
            action.Fulfill ();

            // Add call to manager
            CallManager.Calls.Add (activeCall);
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Он создает экземпляр класса `ActiveCall` класса (для хранения сведений о вызове выполняется) и заполняет вызываемого пользователя. `StartingConnectionChanged` И `ConnectedChanged` события используются для мониторинга и жизненный цикл исходящего вызова. Вызов запускается, и система в курсе, что действие выполнен.

### <a name="ending-an-outgoing-call"></a>Завершение исходящего вызова

Когда пользователь закончил исходящего звонка и надежды, чтобы завершить процесс, можно использовать следующий код:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void EndCall (ActiveCall call)
{
    // Build action
    var endCallAction = new CXEndCallAction (call.UUID);

    // Create transaction
    var transaction = new CXTransaction (endCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Если создает `CXEndCallAction` с UUID вызова для завершения, объединяет его в `CXTransaction` то есть отправляемые системы с помощью `RequestTransaction` метод `CXCallController` класса. 

## <a name="additional-callkit-details"></a>Дополнительные сведения о CallKit

В этом разделе будут рассмотрены некоторые дополнительные сведения, которые разработчик будет необходимо принять во внимание при работе с CallKit, такие как:

- Конфигурация поставщика
- Действие ошибки
- Системные ограничения
- Аудио VOIP

### <a name="provider-configuration"></a>Конфигурация поставщика

Поставщик конфигурации позволяет приложению iOS 10 VOIP настройку пользовательского интерфейса (внутри собственного пользовательского интерфейса во время вызова) при работе с CallKit.

Приложение может сделать следующие настройки:

- Отображать локализованное имя.
- Включите видео позвонить в службу поддержки.
- Настройте кнопки на вызов в пользовательском Интерфейсе, предоставляя собственный значок образа шаблона. Взаимодействие с пользователем с пользовательскими кнопками отправляются непосредственно в приложение для обработки. 

### <a name="action-errors"></a>Действие ошибки

iOS 10 VOIP приложениям с помощью CallKit требуется обрабатывать корректное поведение действия и пользователей о состояния действия, все время. 

Рассмотрим следующий пример во внимание:

1. Приложение получило вызвать действие при запуске и начал процесс инициализации новый вызов VOIP с помощью его сетевого взаимодействия.
2. Из-за ограниченного или сетевые функции обмена данными это соединение завершается ошибкой.
3. Приложение *необходимо* отправки **ошибкой** отправляет сообщение вызвать действие при запуске (`Action.Fail()`) для информирования системы сбоя.
4. Это позволяет системе для информирования пользователя о состоянии вызова. Например, чтобы отобразить пользовательский Интерфейс сбой вызова.

Кроме того, приложение iOS 10 VOIP должны отвечать на _ошибки времени ожидания_ , может произойти, если ожидаемое действие не может обрабатываться в течение заданного времени. Каждый тип действия, предоставляемые CallKit имеет максимальное значение времени ожидания, связанные с ней. Эти значения времени ожидания убедитесь, что любое действие CallKit по запросу пользователя обрабатывается образом реагировать, что помогает поддерживать актуальность ОС динамична и также.

Существует несколько способов для делегата, поставщика (`CXProviderDelegate`) для правильной обработки этой ситуации время ожидания также должен быть переопределен.

### <a name="system-restrictions"></a>Системные ограничения

На основе текущего состояния устройства iOS под управлением iOS 10 приложение VOIP, определенные системные ограничения может быть инициирован.

Например входящий звонок VOIP можно ограничить системой, если:

1. Пользователь, вызвавший включен список заблокированных вызывающего пользователя.
2. Устройство iOS пользователя находится в режиме-Not-беспокоить.

Если любой из этих ситуаций ограничивается звонок по протоколу VOIP, используйте следующий код для обработки его:

```csharp
public class ProviderDelegate : CXProviderDelegate
{
...

    public void ReportIncomingCall (NSUuid uuid, string handle)
    {
        // Create update to describe the incoming call and caller
        var update = new CXCallUpdate ();
        update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);
    
        // Report incoming call to system
        Provider.ReportNewIncomingCall (uuid, update, (error) => {
            // Was the call accepted
            if (error == null) {
                // Yes, report to call manager
                CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
            } else {
                // Report error to user here
                if (error.Code == (int)CXErrorCodeIncomingCallError.CallUuidAlreadyExists) {
                    // Handle duplicate call ID
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByBlockList) {
                    // Handle call from blocked user
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByDoNotDisturb) {
                    // Handle call while in do-not-disturb mode
                } else {
                    // Handle unknown error
                }
            }
        });
    }

}
```

### <a name="voip-audio"></a>Аудио VOIP

CallKit предоставляет несколько преимуществ для обработки звука ресурсов, необходимых приложению iOS 10 VOIP во время динамической звонок по протоколу VOIP. Одним из важнейших преимуществ является приложения аудио сеанса у вас появятся повышенные приоритеты при работе в iOS 10. Это тот же уровень приоритета, как встроенные в телефон и FaceTime приложения и этим уровнем приоритета улучшенные будет препятствовать другие работающие приложения прерывать сеанс аудио приложения VOIP.

Кроме того CallKit имеет доступ к другими аудио маршрутизации подсказками, которые могут повысить производительность и обеспечивает возможность интеллектуального перенаправления звука VOIP для отдельных устройств вывода во время динамического вызова на основе предпочтений пользователя и состояния устройства. Например на основе устройств, например наушники bluetooth, активное подключение CarPlay или параметры специальных возможностей.

Во время жизненного цикла типичный VOIP вызов с использованием CallKit, приложение потребуется настроить Stream звук, который предоставит CallKit. Рассмотрим следующий пример:

[![](callkit-images/callkit09.png "Начальная последовательность вызова действия")](callkit-images/callkit09.png#lightbox)

1. Получает вызвать действие при запуске приложения для ответа на входящий звонок.
2. Прежде чем это действие выполняется в приложении, он предоставляет конфигурацию, потребуются для его `AVAudioSession`.
3. Приложение сообщает системе, что действие выполнено.
4. Прежде чем подключается вызов, CallKit предоставляет высоким приоритетом `AVAudioSession` соответствия конфигурации, запрошенный приложением. Приложение будет уведомлен по `DidActivateAudioSession` метод его `CXProviderDelegate`.

## <a name="working-with-call-directory-extensions"></a>Работа с вызова расширения каталогов

При работе с CallKit, _вызова расширения каталогов_ позволяют добавлять номера заблокированных вызовов и определить номера, характерные для заданного приложения VOIP контактов в приложении обратитесь в службу на устройстве iOS.

### <a name="implementing-a-call-directory-extension"></a>Реализация модуля directory вызова

Чтобы реализовать расширение каталога вызовов Call в приложение Xamarin.iOS, сделайте следующее:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Откройте в решение приложения в Visual Studio для Mac.
2. Щелкните правой кнопкой мыши имя решения на **обозревателе решений** и выберите **добавить** > **Добавление нового проекта**.
3. Выберите **iOS** > **расширения** > **вызова расширения каталогов** и нажмите кнопку **Далее** кнопки: 

    [![](callkit-images/calldir01.png "Создание нового расширение каталога вызовов Call")](callkit-images/calldir01.png#lightbox)
4. Введите **имя** расширение и нажмите кнопку **Далее** кнопки: 

    [![](callkit-images/calldir02.png "Введите имя для расширения")](callkit-images/calldir02.png#lightbox)
5. Настройка **имя_проекта** и/или **имя решения** Если требуется и нажмите кнопку **создать** кнопки: 

    [![](callkit-images/calldir03.png "Создание проекта")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Откройте решение приложения в Visual Studio.
2. Щелкните правой кнопкой мыши имя решения на **обозревателе решений** и выберите **добавить** > **Добавление нового проекта**.
3. Выберите **iOS** > **расширения** > **вызова расширения каталогов** и нажмите кнопку **Далее** кнопки: 

    [![](callkit-images/calldir01w.png "Создание нового расширение каталога вызовов Call")](callkit-images/calldir01.png#lightbox)
4. Введите **имя** расширение и нажмите кнопку **ОК** кнопки

-----

При этом будет добавлено `CallDirectoryHandler.cs` класс в проект, который выглядит следующим образом:

```csharp
using System;

using Foundation;
using CallKit;

namespace MonkeyCallDirExtension
{
    [Register ("CallDirectoryHandler")]
    public class CallDirectoryHandler : CXCallDirectoryProvider, ICXCallDirectoryExtensionContextDelegate
    {
        #region Constructors
        protected CallDirectoryHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void BeginRequest (CXCallDirectoryExtensionContext context)
        {
            context.Delegate = this;

            if (!AddBlockingPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add blocking phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 1, null);
                context.CancelRequest (error);
                return;
            }

            if (!AddIdentificationPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add identification phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 2, null);
                context.CancelRequest (error);
                return;
            }

            context.CompleteRequest (null);
        }
        #endregion

        #region Private Methods
        private bool AddBlockingPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to block from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 14085555555, 18005555555 };

            foreach (var phoneNumber in phoneNumbers)
                context.AddBlockingEntry (phoneNumber);

            return true;
        }

        private bool AddIdentificationPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to identify and their identification labels from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 18775555555, 18885555555 };
            string [] labels = { "Telemarketer", "Local business" };

            for (var i = 0; i < phoneNumbers.Length; i++) {
                long phoneNumber = phoneNumbers [i];
                string label = labels [i];
                context.AddIdentificationEntry (phoneNumber, label);
            }

            return true;
        }
        #endregion

        #region Public Methods
        public void RequestFailed (CXCallDirectoryExtensionContext extensionContext, NSError error)
        {
            // An error occurred while adding blocking or identification entries, check the NSError for details.
            // For Call Directory error codes, see the CXErrorCodeCallDirectoryManagerError enum.
            //
            // This may be used to store the error details in a location accessible by the extension's containing app, so that the
            // app may be notified about errors which occurred while loading data even if the request to load data was initiated by
            // the user in Settings instead of via the app itself.
        }
        #endregion
    }
}
```

`BeginRequest` Метод в обработчике Directory вызов нужно будет изменить, чтобы обеспечить требуемую функциональность. В нашем примере выше он пытается установить список заблокированных и доступных номеров в базе данных приложения VOIP контактов. При сбое либо запросов по любой причине, создайте `NSError` опишите проблему и передавать их `CancelRequest` метод `CXCallDirectoryExtensionContext` класса.

Настройка использования заблокированных номера `AddBlockingEntry` метод `CXCallDirectoryExtensionContext` класса. Значения в метод _необходимо_ располагаться в порядке возрастания в числовом виде. Для оптимальной производительности и использования памяти при наличии, что многие номера телефонов, рассмотрим только загрузка подмножество номера в определенный момент времени и использование autorelease пулы для освобождения объектов, выделенных во время каждого пакета, чисел, в который загружаются.

Чтобы сообщить приложению контакта из номера телефонов контактных лиц, известные в приложение VOIP, используйте `AddIdentificationEntry` метод `CXCallDirectoryExtensionContext` класса и укажите номер и метку. Опять же, чисел, переданной методу _необходимо_ располагаться в порядке возрастания в числовом виде. Для оптимальной производительности и использования памяти при наличии, что многие номера телефонов, рассмотрим только загрузка подмножество номера в определенный момент времени и использование autorelease пулы для освобождения объектов, выделенных во время каждого пакета, чисел, в который загружаются.

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается новый API CallKit, Apple, выпущенная в iOS 10 и способы его реализации в приложениях Xamarin.iOS VOIP. Он показано, как CallKit позволяет приложению интегрировать в системы iOS, как он обеспечивает те же функции с помощью встроенных приложений (например, телефоне) и как оно увеличивается видимость приложения на протяжении всего операций ввода-вывода в местоположения, такие как блокировки и Главная экраны через Siri взаимодействия, а через приложения контакты.

## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
