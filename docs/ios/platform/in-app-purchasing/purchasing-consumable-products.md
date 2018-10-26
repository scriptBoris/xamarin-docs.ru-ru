---
title: Приобретение готовых к использованию продуктов в Xamarin.iOS
description: В этом документе описываются готовых к использованию продуктов в Xamarin.iOS. Готовых к использованию продуктов — одноразовых части функции, такие как валюты в игре.
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: b55465a700974e0ce5ceb8893d96311d920e04ae
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123652"
---
# <a name="purchasing-consumable-products-in-xamarinios"></a>Приобретение готовых к использованию продуктов в Xamarin.iOS

Готовых к использованию продуктов, которые проще всего реализовать, так как нет необходимости «восстановить». Они полезны для таких продуктов, как валюты в игре или одноразовых определенную функцию. Пользователей можно повторно купить готовых к использованию продуктов over и over еще раз.

## <a name="built-in-product-delivery"></a>Встроенные продукта доставки

Примером кода в этом документе демонстрируется встроенные продуктов: коды продуктов «зашиты» в приложение так, как они тесно связаны в код, чтобы разблокировать компонент после оплаты. Процесс покупки можно представить следующим образом:   
   
[![Закупок визуализации процесса](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 Ниже приведен базовый рабочий процесс.   
   
 1. Приложение добавляет `SKPayment` в очередь. При необходимости, пользователю будет предложено ввести свой идентификатор Apple, а запрос на подтверждение оплаты.   
   
 2. StoreKit отправляет запрос на сервер для обработки.   
   
 3. После завершения транзакции, сервер отвечает с уведомлением о транзакции.   
   
 4. `SKPaymentTransactionObserver` Подкласс получает приемки и обрабатывает его.   
   
 5. Приложение позволяет продукта (путем обновления `NSUserDefaults` или другой механизм) и затем вызывает его StoreKit `FinishTransaction`.

Есть другой тип рабочего процесса — *продуктов Server-Delivered* — то есть обсуждается далее в документе (см. в разделе *проверки поступления и продуктов Server-Delivered*).

## <a name="consumable-products-example"></a>Пример готовых к использованию продуктов

[Кода InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) содержит проект с именем *расходных* , реализует базовый «в игре валюта» (называемые «повозиться кредиты»). Пример показано, как реализовать два продукта покупки в приложении, обеспечивающая купить как «monkey кредиты» нужно – в реальном приложении также будет каким-либо образом суммы расходов их!   
   
   
   
 Приложение отображается на этих снимках экрана — каждой покупки, добавляет дополнительные «monkey кредиты» баланс пользователя:   
   
   
   
 [![Каждой покупки, добавляет дополнительные кредиты monkey баланс пользователей](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   
   
   
   
 Взаимодействие между пользовательские классы, StoreKit и App Store будет выглядеть следующим образом:   
   
   
   
 [![Взаимодействие между пользовательские классы, StoreKit и App Store](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

&nbsp;

### <a name="viewcontroller-methods"></a>Методы ViewController

Помимо свойства и методы, необходимые для получения сведений о продуктах контроллер представления требуется дополнительное уведомление наблюдателей для прослушивания уведомлений, связанных с покупки. Это просто `NSObjects` , будут зарегистрированы и удалены в `ViewWillAppear` и `ViewWillDisappear` соответственно.

```csharp
NSObject succeededObserver, failedObserver;
```

Конструктор также создает `SKProductsRequestDelegate` подкласс ( `InAppPurchaseManager`), в свою очередь создает и регистрирует `SKPaymentTransactionObserver` ( `CustomPaymentObserver`).   
   
   
   
 В первой части обработки транзакции покупки в приложении должен обрабатывать нажатие кнопки, когда пользователь хочет приобрести что-то, как показано в следующем коде из примера приложения:

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

   
   
 Во второй части пользовательского интерфейса обрабатывает уведомления, то транзакция выполнена успешно, в данном случае путем обновления отображается баланс:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

Заключительную часть пользовательского интерфейса отображается сообщение, если транзакция отменяется для какой-либо причине. В примере кода в окне вывода просто записывается сообщение:

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

В дополнение к этим методам в контроллере представления, готовых к использованию продукта транзакции покупки требуется код на `SKProductsRequestDelegate` и `SKPaymentTransactionObserver`.

### <a name="inapppurchasemanager-methods"></a>Методы InAppPurchaseManager

В образце кода реализуется несколько приобрести связанных методов класса InAppPurchaseManager, включая `PurchaseProduct` метод, который создает `SKPayment` экземпляра и добавляет его в очередь для обработки:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

Добавление платежа в очередь является асинхронной операцией. Приложение получает управление, пока StoreKit обрабатывает транзакции и отправляет его на серверах компании Apple. Это на этом этапе будет проверять, iOS, пользователь вошел в App Store и запрашивать ей идентификатор Apple ID и пароля, при необходимости.   
   
   
   
 Предполагая, что пользователь успешно проходит аутентификацию с помощью App Store и соглашается с тем к транзакции, `SKPaymentTransactionObserver` будет получен ответ в StoreKit и вызвать следующий метод для выполнения транзакции и завершить его.

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

Последний шаг — чтобы уведомить о StoreKit успешно выполненное транзакции, путем вызова `FinishTransaction`:

```csharp
public void FinishTransaction(SKPaymentTransaction transaction, bool wasSuccessful)
{
   // remove the transaction from the payment queue.
   SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);  // THIS IS IMPORTANT - LET'S APPLE KNOW WE'RE DONE !!!!
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {transaction},new NSObject[] {new NSString("transaction")});
       if (wasSuccessful) {
           // send out a notification that we've finished the transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionSucceededNotification, this, userInfo);
       } else {
           // send out a notification for the failed transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionFailedNotification, this, userInfo);
       }
   }
}
```

После поставки продукта `SKPaymentQueue.DefaultQueue.FinishTransaction` должен вызываться для удаления транзакции из очереди оплаты.

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>Методы SKPaymentTransactionObserver (CustomPaymentObserver)

Вызовы StoreKit `UpdatedTransactions` метод, когда он получает ответ от серверы Apple и передает массив `SKPaymentTransaction` объекты для проверки кода. Метод перебирает каждую транзакцию и выполняет другую функцию, исходя из состояния транзакции (как показано ниже):

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
           case SKPaymentTransactionState.Purchased:
              theManager.CompleteTransaction(transaction);
               break;
           case SKPaymentTransactionState.Failed:
              theManager.FailedTransaction(transaction);
               break;
           default:
               break;
       }
   }
}
```

`CompleteTransaction` Метод изложено ранее в этом разделе — она сохраняет сведения о покупке для `NSUserDefaults`, завершает транзакцию с StoreKit и наконец уведомляет пользовательский Интерфейс для обновления.

### <a name="purchasing-multiple-products"></a>Приобретение нескольких продуктов

Если имеет смысл в приложении на покупку нескольких продуктов, используйте `SKMutablePayment` класса и установите в поле Quantity:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

Код, обрабатывающий завершенной транзакции также необходимо запросить свойство количество правильно выполнить покупки:

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   var qty = transaction.Payment.Quantity;
   if (productId == ConsumableViewController.Buy5ProductId)
       CreditManager.Add(5 * qty);
   else if (productId == ConsumableViewController.Buy10ProductId)
       CreditManager.Add(10 * qty);
   else
       Console.WriteLine ("Shouldn't happen, there are only two products");
   FinishTransaction(transaction, true);
}
```

Пользователь приобретает несколько экземпляров, оповещение подтверждение StoreKit будет отображать количество, цена за единицу и общая стоимость они будет взиматься плата, как показано на следующем снимке экрана:

[![Подтверждение покупки](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>Обработка сбоев сети

Покупки из приложений требуют работающее подключение к сети для StoreKit для взаимодействия с серверами Apple. Если сетевое подключение не доступен, то покупки из приложений будет недоступна.

### <a name="product-requests"></a>Запросы продукта

Если сеть недоступна, делая `SKProductRequest`, `RequestFailed` метод `SKProductsRequestDelegate` подкласс ( `InAppPurchaseManager`) будет вызываться, как показано ниже:

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {error},new NSObject[] {new NSString("error")});
       // send out a notification for the failed transaction
       NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerRequestFailedNotification, this, userInfo);
   }
}
```

Затем ViewController прослушивает уведомление и отображает сообщение в кнопках покупку:

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

Так как сетевое подключение может быть временной на мобильных устройствах, приложения может хотите отслеживать состояние сети, используя платформу SystemConfiguration и повторите попытку, когда доступно соединение сети. Обратитесь к Apple или, что использует его.

### <a name="purchase-transactions"></a>Транзакции покупки

Сохранит очереди StoreKit оплаты и прямой покупки запрашиваемый, если это возможно, поэтому последствия сбоя в работе сети будет зависят от того, при сбое сети во время покупки.   
   
   
   
 Если ошибка возникает во время транзакции, `SKPaymentTransactionObserver` подкласс ( `CustomPaymentObserver`) будет иметь `UpdatedTransactions` метод с именем и `SKPaymentTransaction` класс будет в состоянии сбоя.

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
           case SKPaymentTransactionState.Purchased:
               theManager.CompleteTransaction(transaction);
               break;
           case SKPaymentTransactionState.Failed:
               theManager.FailedTransaction(transaction);
               break;
           default:
               break;
       }
   }
}
```

`FailedTransaction` Метод обнаруживает ли ошибка вызвана Отмена пользователем, как показано ниже:

```csharp
public void FailedTransaction (SKPaymentTransaction transaction)
{
   //SKErrorPaymentCancelled == 2
   if (transaction.Error.Code == 2) // user cancelled
       Console.WriteLine("User CANCELLED FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   else // error!
       Console.WriteLine("FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   FinishTransaction(transaction,false);
}
```

Даже если транзакция дает сбой, `FinishTransaction` метод должен вызываться для удаления транзакции из очереди оплаты:

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

В примере кода затем отправляет уведомление, чтобы ViewController может отобразить сообщение. Приложения не должно отображать дополнительный сообщение, если пользователь отменяет транзакцию. Другие коды ошибок, которые могут возникнуть включают:

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>Обработка ограничений

**Параметры > Общие > ограничения** особенностью iOS позволяет заблокировать некоторые возможности устройства.   
   
   
   
 Вы можете запросить, разрешено ли пользователю для совершения покупок в приложении с помощью `SKPaymentQueue.CanMakePayments` метод. Если он возвращает значение false затем пользователь не может получить доступ к покупки из приложений. StoreKit автоматически отобразит сообщение об ошибке для пользователя при попытке выполнения покупки. Установив это значение приложения вместо этого можно скрыть кнопки покупки или выполнить определенное действие, чтобы помочь пользователю.   
   
   
   
 В `InAppPurchaseManager.cs` файл `CanMakePayments` метод создает оболочку для функции StoreKit следующим образом:

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

Чтобы протестировать этот метод, используйте **ограничения** компонент iOS, чтобы отключить **покупки из приложений**:   
   
   
   
 [![Использовать функцию ограничения операций ввода-вывода для отключения покупки из приложений](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   
   
   
   
 Этот пример кода из `ConsumableViewController` реагирует на `CanMakePayments` возвращает значение false, отобразив **AppStore отключена** текст на кнопках отключено.

```csharp
// only if we can make payments, request the prices
if (iap.CanMakePayments()) {
   // now go get prices, if we don't have them already
   if (!pricesLoaded)
       iap.RequestProductData(products); // async request via StoreKit -> App Store
} else {
   // can't make payments (purchases turned off in Settings?)
   // the buttons are disabled by default, and only enabled when prices are retrieved
   buy5Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
   buy10Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
}
```

Приложение выглядит это when **покупки из приложений** функция ограничен — кнопки покупки будут отключены.   
   
   
   
 [![Приложение выглядит при покупки в приложении, функция ограничения покупки кнопки отключены](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   
   
   
   

Сведения о продукте по-прежнему может быть, запрашиваемые при `CanMakePayments` имеет значение false, поэтому приложение по-прежнему можно получить и отобразить цены. Это означает, что если мы удалили `CanMakePayments` проверки из кода, кнопки покупки придется по-прежнему будет активным, хотя при попытке покупку пользователь увидит сообщение, **покупки из приложений не допускаются** (созданных в ходе StoreKit Когда очередь оплата осуществляется):   
   
   
   
 [![Покупки из приложений не допускаются.](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   
   
   
   
 Реальные приложения может занять другой подход к обработке ограниченного использования программ, таких как скрытие кнопки полностью и возможно предлагает более подробное сообщение чем оповещение, которое StoreKit показано автоматически.

