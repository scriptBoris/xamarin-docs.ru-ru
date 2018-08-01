---
title: Общие сведения о StoreKit и получении сведения о продукте в Xamarin.iOS
description: Этот документ содержит общие сведения о StoreKit. Он описывает классы, используемые с StoreKit тестирование StoreKit взаимодействий, отображения продуктов для продажи, обработка недопустимый продуктов и отображения локализованных цены.
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 964b97e82db8e79cb32598d0c955fac3ab122314
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787228"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>Общие сведения о StoreKit и получении сведения о продукте в Xamarin.iOS

На снимке экрана ниже показан пользовательский интерфейс для покупок в приложении.
Перед любой транзакции производится, приложение должно получить цену и описание для отображения продукта. Когда пользователь нажимает **купить**, приложение запрашивает StoreKit управляющему диалоговое окно подтверждения и идентификатор Apple ID входа. При условии, что затем транзакция завершается успешно, StoreKit уведомляет код приложения, который необходимо сохранить результат транзакции и предоставить пользователю доступ к покупке.   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "В коде приложения, который необходимо сохранить результат транзакции и предоставить пользователю доступ к покупке уведомляет StoreKit")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Классы

Реализация покупки из приложений требуются следующие классы из StoreKit framework:   
   
 **SKProductsRequest** — запрос на StoreKit для утвержденных продуктов для продажи (магазина). Можно настроить количество кодов продуктов.

-   **SKProductsRequestDelegate** — объявляет методы для обработки продукта запросов и ответов. 
-   **SKProductsResponse** — отправлен обратно на делегат из StoreKit (магазина). Содержит SKProducts, соответствует продукта, отправленным идентификаторы с запросом. 
-   **SKProduct** — продукта, полученных из StoreKit (, который настроен в iTunes Connect). Содержит сведения о продукте, такие как идентификатор продукта, название, описание, цену. 
-   **SKPayment** — создан код продукта и добавленных в очередь оплаты для выполнения покупки. 
-   **SKPaymentQueue** — оплаты запросы в очереди отправки в Apple. Уведомления активируются в результате каждого обработки платежа. 
-   **SKPaymentTransaction** — представляет завершенной транзакции (заявку на покупку, обрабатываемых App Store и отправляются обратно в приложение через StoreKit). Транзакция может быть Куплено, восстановленные или сбой. 
-   **SKPaymentTransactionObserver** — настраиваемый подкласс, в котором реагирует на события, создаваемые в очереди StoreKit оплаты. 
-   **StoreKit операции являются асинхронными** – после запуска SKProductRequest или SKPayment добавляется в очередь, управление возвращается в код. StoreKit будет вызывать методы в подкласс SKProductsRequestDelegate или SKPaymentTransactionObserver при получении данных от компании Apple серверов. 


Следующей схеме показаны связи между различными классами StoreKit (абстрактных классов должен быть реализован в приложении):   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "Связи между различными классами абстрактные классы StoreKit должен быть реализован в приложении")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 Эти классы являются более подробно далее в этом документе.

## <a name="testing"></a>Тестирование

Большинство операций StoreKit реального устройства требуется для тестирования. Получение сведений о продукта (т. е. Цена &amp; описание) будут работать в имитаторе, но покупки и операций восстановления отобразится сообщение об ошибке (например, код FailedTransaction = 5002 произошла неизвестная ошибка).

Примечание: StoreKit не работает в эмуляторе iOS. При запуске приложения в симуляторе iOS, StoreKit заносит в журнал предупреждение, если приложение пытается получить очередь оплаты. Тестирование в хранилище необходимо сделать на фактических устройствах.   
   
   
   
 Важно: Выполняют вход с помощью учетной записи в приложении параметры тестирования. Можно использовать параметры приложения для входа из любой существующей учетной записи Apple ID, то необходимо подождать, чтобы запрос *в последовательности на покупку из приложения* для входа в систему с использованием тестирования Apple ID.   
   
   
   

При попытке войти в действительности с тестовой учетной записи, он будет автоматически преобразован реальные идентификатор Apple. Этой учетной записи больше не будет работать для тестирования.

Для тестирования кода StoreKit необходимо выхода регулярного iTunes тестовой учетной записи и имя входа с помощью специальных тестовую учетную запись (созданных в iTunes Connect), связанного с хранилище теста. Чтобы выйти из текущей учетной записи посещение **параметры > iTunes и магазин приложений** как показано ниже:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "Чтобы выйти из посещение текущей учетной записи iTunes параметры и хранилище приложений")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
затем войдите с помощью тестовой учетной записи *при запросе StoreKit в приложении*:



Создание тестовых пользователей в iTunes Connect щелкните **пользователей и ролей** на главной странице.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "Чтобы создать тестовых пользователей в iTunes Connect щелкните пользователям и ролям на главной странице")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Выберите **тест-инженеры "песочницы"**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "Выбор тест-инженеры \"песочницы\"")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

Отображается список существующих пользователей. Можно добавить нового пользователя или удалить существующую запись. Не поддерживает портала (в данный момент) позволяют просмотреть или изменить имеющиеся проверки пользователей, поэтому рекомендуется оставить хорошо запись каждого тестового пользователя, создается (особенно пароль назначения). После удаления тестового пользователя адрес электронной почты нельзя использовать повторно для другой учетной записи теста.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "Отображается список существующих пользователей")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 Новый тестовых пользователей имеют одинаковые атрибуты для реальных Apple ID (например, имя, пароль, секретный вопрос и ответ). Записывайте все сведения, введенные здесь. **Магазине iTunes выберите** поле определит, какие валюты и покупки из приложений язык будет использоваться при вошедшего в систему как пользователь.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "Определяет поле магазина iTunes выберите валюты и язык для покупок в приложении пользователя")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Получение сведений о продукта

Первым шагом в продаваемые продукты покупок в приложении отображается: получение текущую цену и описание из магазина приложений для отображения.   
   
   
   
 Независимо от того, какой тип продуктов приложения продает (который может быть использован, потребляемые не или тип подписки) процесс извлечения сведения о продукте для отображения одинаково. Код InAppPurchaseSample к этой статье содержит проект с именем *материалы* , показано, как получить сведения о рабочей среде для отображения. Здесь показано, как:

-  Реализация `SKProductsRequestDelegate` и реализовать `ReceivedResponse` абстрактный метод. Пример кода вызывает это `InAppPurchaseManager` класса. 
-  Уточните StoreKit, чтобы узнать, разрешено ли платежи (с помощью `SKPaymentQueue.CanMakePayments` ). 
-  Создать экземпляр `SKProductsRequest` с коды продуктов, которые были определены в iTunes Connect. Это делается в приведенном примере `InAppPurchaseManager.RequestProductData` метод. 
-  Вызывать метод Start в `SKProductsRequest` . Это вызывает асинхронный вызов серверы App Store. Делегат ( `InAppPurchaseManager` ) будет вызван назад с результатами. 
-  Делегат ( `InAppPurchaseManager` ) `ReceivedResponse` метод обновляет пользовательский Интерфейс с данными, полученными от магазина приложений (цены продуктов и описания или сообщения о продуктах недопустимый). 

Общее взаимодействие выглядит следующим образом ( **StoreKit** встроена в iOS и **App Store** представляет серверы Apple):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Получение информации о продукте графа")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Отображение данных продукта, пример

[InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *материалы* образце кода показано, как можно получить сведения о продукте. В основном окне образца отображаются сведения по двум видам продуктов, которые извлекаются из магазина приложений:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "В основном окне отображаются продукты сведения, полученные из магазина приложений")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 Ниже более подробно описан в образце кода для извлечения и отображения сведений о продукте.


#### <a name="viewcontroller-methods"></a>Методы ViewController

`ConsumableViewController` Класс будет использоваться для управления отображением цены по двум видам продуктов, идентификаторы которых продукта жестко запрограммированы в классе.

```csharp
public static string Buy5ProductId = "com.xamarin.storekit.testing.consume5credits",
   Buy10ProductId = "com.xamarin.storekit.testing.consume10credits";
List<string> products;
InAppPurchaseManager iap;
public ConsumableViewController () : base()
{
   // two products for sale on this page
   products = new List<string>() {Buy5ProductId, Buy10ProductId};
   iap = new InAppPurchaseManager();
}
```

Класс уровень должен быть объявлен NSObject, который будет использоваться для установки `NSNotificationCenter` наблюдателя:

```csharp
NSObject priceObserver;
```

В методе ViewWillAppear наблюдатель и назначается с помощью центра уведомлений по умолчанию:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 В конце `ViewWillAppear` метод, вызовите `RequestProductData` метод, чтобы инициировать запрос StoreKit. После этого запрос StoreKit асинхронно свяжется с серверами Apple для получения сведений и добавить его обратно в ваше приложение. Это достигается за счет `SKProductsRequestDelegate` подкласс ( `InAppPurchaseManager`), о котором рассказывается в следующем разделе.

```csharp
iap.RequestProductData(products);
```

Код, чтобы показать цену и описание просто извлекает сведения из SKProduct и присваивает его к элементам управления UIKit (Обратите внимание, что мы можем отобразить `LocalizedTitle` и `LocalizedDescription` — StoreKit автоматически разрешает правильный текст и цены на основании параметры учетной записи пользователя). В области уведомлений, созданных ранее принадлежит следующий код:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
   var info = notification.UserInfo;
   if (info.ContainsKey(NSBuy5ProductId)) {
       var product = (SKProduct) info.ObjectForKey(NSBuy5ProductId);
       buy5Button.Enabled = true;
       buy5Title.Text = product.LocalizedTitle;
       buy5Description.Text = product.LocalizedDescription;
       buy5Button.SetTitle("Buy " + product.Price, UIControlState.Normal); // price display should be localized
   }
}
```

Наконец `ViewWillDisappear` метод должен обеспечивать удаляется наблюдателя:

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Методы SKProductRequestDelegate (InAppPurchaseManager)

`RequestProductData` Метод вызывается, когда приложение желает получать цены продуктов и другую информацию. Он выполняет синтаксический анализ коллекции коды продуктов в правильный тип данных, затем создает `SKProductsRequest` с этой информацией. Вызов метода Start вызывает сетевой запрос к серверы Apple. Запрос будет выполняться асинхронно и вызвать `ReceivedResponse` метод делегата при успешном завершении.

```csharp
public void RequestProductData (List<string> productIds)
{
   var array = new NSString[productIds.Count];
   for (var i = 0; i < productIds.Count; i++) {
       array[i] = new NSString(productIds[i]);
   }
   NSSet productIdentifiers = NSSet.MakeNSObjectSet<NSString>(array);
   productsRequest = new SKProductsRequest(productIdentifiers);
   productsRequest.Delegate = this; // for SKProductsRequestDelegate.ReceivedResponse
   productsRequest.Start();
}
```

iOS автоматически маршрутизации запроса «песочницы» или «рабочий» версии App Store, в зависимости от того, какой профиль подготовки, приложение выполняется с параметрами –, поэтому при разработке и тестировании приложения будет иметь запрос на доступ для каждого продукта настроить в iTunes Connect (даже те, которые еще не был отправлен или одобрен Apple). Когда приложение находится в рабочей среде, StoreKit запросов будет возвратить только сведения о **утверждено** продуктов.   
   
   
   
 `ReceivedResponse` Переопределенный метод вызывается после получения ответа серверы Apple с данными. Поскольку это вызывается в фоновом режиме, код должен синтаксический анализ допустимых данных и использовать уведомления для отправки информации о продукте любой ViewControllers, что «прослушивают» уведомления о. Ниже приведен код для сбора сведений продукта и отправить уведомление:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   SKProduct[] products = response.Products;
   NSDictionary userInfo = null;
   if (products.Length > 0) {
       NSObject[] productIdsArray = new NSObject[response.Products.Length];
       NSObject[] productsArray = new NSObject[response.Products.Length];
       for (int i = 0; i < response.Products.Length; i++) {
           productIdsArray[i] = new NSString(response.Products[i].ProductIdentifier);
           productsArray[i] = response.Products[i];
       }
       userInfo = NSDictionary.FromObjectsAndKeys (productsArray, productIdsArray);
   }
   NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerProductsFetchedNotification, this, userInfo);
}
```

Несмотря на то, что не показано на схеме `RequestFailed` , следует также переопределить метод, чтобы предоставлять отзыв пользователя в случае серверов магазина недоступны (или некоторых других ошибок). В примере кода просто записывает в консоль, но выберите реальному приложению выполнить запрос, `error.Code` свойство и реализовать пользовательское поведение (например, предупреждение для пользователя).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

На этом снимке экрана показан пример приложения, сразу после загрузки (если доступен не сведения о продукте):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "Пример приложения, сразу после загрузки, когда отсутствует информация о продукции")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Недопустимый продуктов

`SKProductsRequest` Могут также возвращать список недопустимый идентификатор продукта. Недопустимый продуктов обычно возвращаются из-за одного из следующих:   
   
   
   
 **Идентификатор продукта были ошибки при вводе** — принимаются только допустимые идентификаторы продукта.   
   
 **Продукт не был утвержден** — во время тестирования, могут быть возвращены все товары, которые будут удалены для продажи `SKProductsRequest`; Однако в рабочей среде, возвращаются только утвержденные продукты.   
   
 **Идентификатор приложения не является явным** — идентификаторы приложений подстановочный знак (отмеченные звездочкой) не позволяют приобрести в приложении.   
   
 **Неправильный профиль подготовки** — при внесении изменений в конфигурацию приложения на портале подготовки (таких как включение покупки из приложений), не забудьте повторно сформировать и использование корректного профиля подготовки, при построении приложения.   
   
 **Контракт платных приложений iOS не на месте** — StoreKit функции не будут работать вообще, если отсутствует допустимый контракт для учетной записи разработчика Apple.   
   
 **Двоичный файл находится в состоянии Отклонено** – Если имеется ранее отправленные двоичные данные в состоянии Отклонено (или группой App Store или разработчиком) StoreKit функции не будет работать.

`ReceivedResponse` Метод в образце кода выводит недопустимый продуктов на консоль:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>Отображение локализованных цены

Цена уровней указать цену для каждого продукта для всех магазинов международных приложений. Чтобы убедиться в правильном отображении цены по каждой валюте, используйте следующий метод расширения (определенные в `SKProductExtension.cs`) вместо свойства Price каждого `SKProduct`:

```csharp
public static class SKProductExtension {
   public static string LocalizedPrice (this SKProduct product)
   {
       var formatter = new NSNumberFormatter ();
       formatter.FormatterBehavior = NSNumberFormatterBehavior.Version_10_4;  
       formatter.NumberStyle = NSNumberFormatterStyle.Currency;
       formatter.Locale = product.PriceLocale;
       var formattedString = formatter.StringFromNumber(product.Price);
       return formattedString;
   }
}
```

Код, который задает заголовок кнопки используется метод расширения следующим образом:

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

С помощью двух разных iTunes тестовые учетные записи (один для американского хранилища) и один для японского хранилища результатов на следующих снимках экрана:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Два разных iTunes тестовые учетные записи, показывающая конкретные результаты языка")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 Обратите внимание, что хранилище влияет на язык, который используется для валюты сведения и цену продукта, а устройство языковой параметр влияет метки и другие локализованное содержимое.   
   
   
   
 Отозвать учетной записи, необходимо, чтобы использовать другое хранилище тестового **выйти** в **параметры > iTunes и приложение магазина** и повторно запустите приложение, войдите под другой учетной записью. Чтобы изменить язык в устройстве перейдите к **параметры > Общие > International > языка**.

