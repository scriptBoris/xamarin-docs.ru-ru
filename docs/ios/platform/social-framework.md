---
title: Платформа для социальных сетей в Xamarin.iOS
description: Платформа для социальных сетей предоставляет единый API для взаимодействия с социальными сетями, включая Twitter и Facebook, а также SinaWeibo для пользователей в Китае.
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 08ccd5b5ac78e82bf745764d70e59d2db9ec6776
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115709"
---
# <a name="social-framework-in-xamarinios"></a>Платформа для социальных сетей в Xamarin.iOS

_Платформа для социальных сетей предоставляет единый API для взаимодействия с социальными сетями, включая Twitter и Facebook, а также SinaWeibo для пользователей в Китае._

С помощью социальных сетей платформы позволяет приложениям взаимодействовать с социальными сетями из одного API без необходимости управлять проверкой подлинности. Он включает системы, предоставляемые контроллера представления для составления сообщения, а также абстракции, который разрешает использование API каждого социальную сеть по протоколу HTTP.

> [!IMPORTANT]
> API кросс платформенных соединиться с различными социальными сетями, см. в разделе [Xamarin.Social](http://components.xamarin.com/view/xamarin.social/) компонент в Store компонент Xamarin.

## <a name="connecting-to-twitter"></a>Подключение к Twitter

### <a name="twitter-account-settings"></a>Параметры учетной записи Twitter

Чтобы подключиться к Twitter, используя платформу социальных сетей, учетной записи необходимо настроить в настройках устройства как показано ниже:

 [![](social-framework-images/twitter01.png "Параметры учетной записи Twitter")](social-framework-images/twitter01.png#lightbox)

После ввода учетной записи и проверить с помощью Twitter, любое приложение на устройстве, платформа для социальных сетей классы для доступа к Twitter будет использовать эту учетную запись.

### <a name="sending-tweets"></a>Отправка Твитов

Платформа для социальных сетей включает контроллер с именем `SLComposeViewController` , отображается представление предоставленном системой для редактирования и отправка твита. На следующем рисунке показан пример этого представления:

 [![](social-framework-images/twitter02.png "На этом снимке экрана показан пример SLComposeViewController")](social-framework-images/twitter02.png#lightbox)

Чтобы использовать `SLComposeViewController` с Twitter, необходимо создать экземпляр контроллера путем вызова `FromService` метод с `SLServiceType.Twitter` как показано ниже:

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

После `SLComposeViewController` возвращается экземпляр, его можно использовать для представления пользовательского интерфейса для публиковать в Twitter. Тем не менее, прежде всего следует — в этом случае проверьте доступность социальной сети Twitter путем вызова `IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` никогда не отправляет твит напрямую без участия пользователя. Тем не менее его можно инициализировать с помощью следующих методов:

-   `SetInitialText` — Добавляет исходный текст для отображения в твите. 
-  `AddUrl` — Добавляет URL-адрес твита.
-  `AddImage` — Добавляет изображение твита.


После инициализации, вызвав `PresentVIewController` отображает представление, созданное `SLComposeViewController`. Пользователь может затем при необходимости изменить и отправки твита или отмените их отправкой. В любом случае контроллер должен закрываться в `CompletionHandler`, где результат можно также проверить на предмет твита было отправлено отменена, как показано ниже:

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>Пример твита

В следующем коде показано использование `SLComposeViewController` Чтобы создать представление, используемый для отправки твита:

```csharp
using System;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _twitterComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
        #endregion

        #region Computed Properties
        public bool isTwitterAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Twitter); }
        }

        public SLComposeViewController TwitterComposer {
            get { return _twitterComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            SendTweet.Enabled = isTwitterAvailable;
        }
        #endregion

        #region Actions
        partial void SendTweet_TouchUpInside (UIButton sender)
        {
            // Set initial message
            TwitterComposer.SetInitialText ("Hello Twitter!");
            TwitterComposer.AddImage (UIImage.FromFile ("Icon.png"));
            TwitterComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (TwitterComposer, true, null);
        }
        #endregion
    }
}
```

### <a name="calling-twitter-api"></a>Вызов Twitter API

Платформа для социальных сетей также включает поддержку выполнения HTTP-запросов к социальным сетям. Он включает в себя запрос в `SLRequest` класс, который используется для целевого API определенной социальной сети.

Например следующий код выполняет запрос к Twitter, чтобы получить общедоступный временной шкалы (развернув на приведенном выше коде):

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _twitterAccount;
#endregion

#region Computed Properties
public ACAccount TwitterAccount {
    get { return _twitterAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    SendTweet.Enabled = isTwitterAvailable;
    RequestTwitterTimeline.Enabled = false;

    // Initialize Twitter Account access 
    var accountStore = new ACAccountStore ();
    var accountType = accountStore.FindAccountType (ACAccountType.Twitter);

    // Request access to Twitter account
    accountStore.RequestAccess (accountType, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestTwitterTimeline.Enabled = true;
            });
        }
    });
}
#endregion

#region Actions
partial void RequestTwitterTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
    var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = TwitterAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

Давайте взглянем на этот код подробно. Во-первых он получает доступ к Store учетной записи и возвращает тип учетной записи Twitter:

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

Затем он запрашивает у пользователя, если ваше приложение может иметь доступ к своей учетной записью Twitter, и, если доступ предоставляется, учетная запись загружается в память и обновлен пользовательский Интерфейс:

```csharp
// Request access to Twitter account
accountStore.RequestAccess (accountType, (granted, error) => {
    // Allowed by user?
    if (granted) {
        // Get account
        _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
        InvokeOnMainThread (() => {
            // Update UI
            RequestTwitterTimeline.Enabled = true;
        });
    }
});
```

Когда пользователь запрашивает данных временной шкалы (нажатием кнопки в пользовательском Интерфейсе), приложение сначала формы запрос на доступ к данным из Twitter:

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```
В этом примере ограничивают возвращаемых результатов, чтобы последние десять записей, включая `?count=10` в URL-адрес. Наконец, он присоединяет запрос к учетной записи Twitter (который был загружен выше) и выполняет вызов к Twitter для получения данных:

```csharp
// Request data
request.Account = TwitterAccount;
request.PerformRequest ((data, response, error) => {
    // Was there an error?
    if (error == null) {
        // Was the request successful?
        if (response.StatusCode == 200) {
            // Yes, display it
            InvokeOnMainThread (() => {
                Results.Text = data.ToString ();
            });
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", response.StatusCode);
            });
        }
    } else {
        // No, display error
        InvokeOnMainThread (() => {
            Results.Text = string.Format ("Error: {0}", error);
        });
    }
});
```

Если данные были успешно загружены, необработанные данные JSON будут отображаться (как в приведенном ниже примере):

[![](social-framework-images/twitter03.png "Пример отображения необработанных данных JSON")](social-framework-images/twitter03.png#lightbox)

В реальном приложении результаты JSON может затем интерпретировать как обычный и результаты, представленные пользователю. См. в разделе [Общие сведения о веб-служб](~/cross-platform/data-cloud/web-services/index.md) сведения о том, как выполнить синтаксический анализ JSON.

## <a name="connecting-to-facebook"></a>Подключение к Facebook

### <a name="facebook-account-settings"></a>Параметры учетной записи Facebook

Подключение к Facebook с помощью платформы социальных сетей очень похож на процесс, используемый для Twitter, показанный выше. Необходимо настроить учетную запись Facebook в настройках устройства, как показано ниже:

[![](social-framework-images/facebook01.png "Параметры учетной записи Facebook")](social-framework-images/facebook01.png#lightbox)

После настройки любого приложения на устройстве, которое использует социальные Framework будет использовать эту учетную запись для подключения к Facebook.

### <a name="posting-to-facebook"></a>Публикация в Facebook

Так как платформа для социальных сетей — универсальный API, предназначенными для доступа к нескольким социальных сетей, код остается практически одинаковых вне зависимости от используемой социальные сети.

Например `SLComposeViewController` можно использовать точно, как показано в примере Twitter, показанного выше, за исключением переключения для вариантов и параметры, относящиеся к Facebook. Пример:

```csharp
using System;
using Foundation;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _facebookComposer = SLComposeViewController.FromService (SLServiceType.Facebook);
        #endregion

        #region Computed Properties
        public bool isFacebookAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Facebook); }
        }

        public SLComposeViewController FacebookComposer {
            get { return _facebookComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            PostToFacebook.Enabled = isFacebookAvailable;
        }
        #endregion

        #region Actions
        partial void PostToFacebook_TouchUpInside (UIButton sender)
        {
            // Set initial message
            FacebookComposer.SetInitialText ("Hello Facebook!");
            FacebookComposer.AddImage (UIImage.FromFile ("Icon.png"));
            FacebookComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (FacebookComposer, true, null);
        }
        #endregion
    }
}
```

При использовании с Facebook, `SLComposeViewController` отображает представление, которое выглядит почти идентичен Twitter, показывающий **Facebook** как заголовок в нашем примере:

[![](social-framework-images/facebook02.png "Отображение SLComposeViewController")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>Вызов API Facebook Graph

Аналогичную Twitter, платформа для социальных сетей `SLRequest` объект может использоваться с Facebook graph API. Например следующий код возвращает сведения из API graph об учетной записи Xamarin (развернув на приведенном выше коде):

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _facebookAccount;
#endregion

#region Computed Properties
public ACAccount FacebookAccount {
    get { return _facebookAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    PostToFacebook.Enabled = isFacebookAvailable;
    RequestFacebookTimeline.Enabled = false;

    // Initialize Facebook Account access 
    var accountStore = new ACAccountStore ();
    var options = new AccountStoreOptions ();
    var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
    accountType = accountStore.FindAccountType (ACAccountType.Facebook);

    // Request access to Facebook account
    accountStore.RequestAccess (accountType, options, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _facebookAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestFacebookTimeline.Enabled = true;
            });
        }
    });

}
#endregion

#region Actions
partial void RequestFacebookTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl ("https://graph.facebook.com/283148898401104");
    var request = SLRequest.Create (SLServiceKind.Facebook, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = FacebookAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

Единственное реальное различие между этим кодом и версии Twitter, представленные выше, является требование Facebook Developer/App конкретного Идентификатору (который можно создать с помощью портала разработчика Facebook) которого должен быть указан как параметр при выполнении запроса:

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

Не удалось установить этот параметр (или использование недопустимого ключа) приведет к ошибку или без возврата данных.

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать платформа для социальных сетей для взаимодействия с Twitter и Facebook. Он показал, где можно настроить учетные записи для каждого социальной сети в настройках устройства. Он также описаны способы использования `SLComposeViewController` чтобы единое представление для публикации в социальных сетях. Кроме того, он проверяет `SLRequest` класс, используемый для вызова API каждого социальной сети.


## <a name="related-links"></a>Связанные ссылки

- [SocialFrameworkDemo (пример)](https://developer.xamarin.com/samples/SocialFrameworkDemo/)
- [Введение в веб-службы](~/cross-platform/data-cloud/web-services/index.md)
