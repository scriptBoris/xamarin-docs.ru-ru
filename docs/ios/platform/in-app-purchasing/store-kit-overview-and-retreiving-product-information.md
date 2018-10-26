---
title: Общие сведения о StoreKit и получении сведения о продукте в Xamarin.iOS
description: Этот документ содержит общие сведения о StoreKit. Он описывает классы, используемые с StoreKit, тестирование взаимодействия StoreKit, отображение продуктов для продажи, обработка недопустимый продуктов и отображение локализованного цены.
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 0dcda2e4fd1ca7773668a0a6fdf46e01f2f0841d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118530"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>Общие сведения о StoreKit и получении сведения о продукте в Xamarin.iOS

На снимках экрана ниже показан пользовательский интерфейс для покупки в приложении.
До выполнения любой транзакции, приложение должно получить цену и описание для отображения этого продукта. Затем при нажатии **купить**, приложение выполняет запрос к StoreKit, который управляет диалоговое окно подтверждения и идентификатор Apple ID входа. При условии, что эта транзакция завершается успешно, StoreKit уведомляет код приложения, которой необходимо хранить результат транзакции и предоставить пользователю доступ к их приобретения.   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "В коде приложения, который необходимо сохранить результат транзакции и предоставить пользователю доступ к покупке уведомляет StoreKit")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Классы

Реализация покупки из приложений требует следующие классы из платформы StoreKit:   
   
 **SKProductsRequest** — запрос на StoreKit для утвержденных продуктов для продажи (App Store). Можно настроить ряд кодов продуктов.

-   **SKProductsRequestDelegate** — объявляет методы для обработки запросов продуктов и ответов. 
-   **SKProductsResponse** — отправленные обратно делегату StoreKit (App Store). Содержит SKProducts, соответствует продукта, которым идентификаторы отправленная с запросом. 
-   **SKProduct** — это продукт, полученные из StoreKit (который настроен в iTunes Connect). Содержит сведения о продукте, такие как идентификатор продукта, название, описание и цена. 
-   **SKPayment** — создан с Идентификатором продукта и добавлено в очередь оплаты для выполнения покупки. 
-   **SKPaymentQueue** — оплаты запросы в очереди отправки на Apple. Уведомления активируются в результате каждого платежа обрабатывается. 
-   **SKPaymentTransaction** — представляет завершенной транзакции (запрос на покупку, обрабатываемых App Store и обратно для отправки приложению через StoreKit). Транзакция может быть Куплено, восстановленная или сбой. 
-   **SKPaymentTransactionObserver** — пользовательский подкласс, который реагирует на события, создаваемые в очереди StoreKit оплаты. 
-   **StoreKit операции являются асинхронными** – после запуска SKProductRequest или SKPayment добавляется в очередь, управление возвращается в код. StoreKit будет вызывать методы в подклассе, SKProductsRequestDelegate или SKPaymentTransactionObserver при получении данных с серверов корпорации Apple. 


Следующей схеме показаны связи между различными классами StoreKit (абстрактные классы должны быть реализованы в приложении).   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "Связи между различных классов абстрактные классы StoreKit должен быть реализован в приложении")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 Эти классы рассматриваются более подробно далее в этом документе.

## <a name="testing"></a>Тестирование

Большинство операций StoreKit требуют реальное устройство для тестирования. Получение сведений о продукта (т. е. Цена &amp; описание) будут работать в симуляторе, но покупки и операций восстановления будет возвращена ошибка (например, код FailedTransaction = 5002 произошла неизвестная ошибка).

Примечание: StoreKit не работает в симуляторе iOS. При запуске приложения в симуляторе iOS, StoreKit заносит в журнал предупреждение, если приложение пытается получить очередь оплаты. Тестирование в хранилище должны выполняться на реальных устройствах.   
   
   
   
 Важно: Не вход с тестовой учетной записи в приложении "настройки". Можно использовать параметры приложения для входа из любой существующей учетной записи Apple ID, то необходимо подождать, чтобы получить запрос *в рамках вашей покупки в приложении последовательности* для входа в систему с помощью теста идентификатора Apple ID.   
   
   
   

При попытке войти в действительности с тестовой учетной записи, он автоматически преобразуются в реальные Apple ID. Этой учетной записи, больше не будет работать для тестирования.

Для тестирования кода StoreKit необходимо выхода регулярных iTunes тестовой учетной записи и входа в систему с учетной записью особые тестовые (создано в iTunes Connect), связанного с хранилище теста. Выйти из текущей учетной записи посещение **параметры > iTunes и App Store** следующим образом:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "Выйдите из текущей учетной записи посещение параметры iTunes и App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
затем войдите с тестовой учетной записи *при запросе StoreKit в приложении*:



Чтобы создать тестовых пользователей в iTunes Connect щелкните **пользователей и ролей** на главной странице.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "Чтобы создать тестовых пользователей в iTunes Connect щелкните пользователями и ролями на главной странице")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Выберите **тест-инженеры "песочницы"**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "Выбор тест-инженеры \"песочницы\"")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

Отображается список существующих пользователей. Можно добавить нового пользователя или удаление существующей записи. На портале нет (сейчас) позволяет просмотреть или изменить имеющиеся тестовых пользователей, поэтому рекомендуется сохранить хороший запись каждого тестового пользователя, который создается (особенно пароль, вы назначаете). После удаления тестового пользователя адрес электронной почты нельзя использовать повторно для другой тестовой учетной записи.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "Отображается список существующих пользователей")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 Новые тестовые пользователи имеют похожие атрибуты для реальных идентификатор Apple ID (например, имя, пароль, секретный вопрос и ответ). Сохраните все сведения, введенные здесь. **Выберите iTunes Store** поле будет определить, какая валюта и языка, покупки из приложений будет использовать при вошедшего в систему от имени этого пользователя.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "Определяет поле iTunes Store выберите валюту и язык для их покупки из приложений пользователя")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Получение сведений о продукте

Первым шагом в продаже продукта покупки в приложении отображается: получение текущую цену и описание из App Store для отображения.   
   
   
   
 Независимо от того, какой тип продуктов приложения продает (машинные команды, не использоваться моделью или тип подписки) процесс получения сведения о продукте для отображения совпадает. InAppPurchaseSample код, прилагаемый к этой статье содержит проект с именем *расходных* показано, как получить сведения о рабочей среде для отображения. Он показывает, как:

-  Реализация интерфейса `SKProductsRequestDelegate` и реализовать `ReceivedResponse` абстрактный метод. Пример кода вызывает этот `InAppPurchaseManager` класса. 
-  Уточните StoreKit, чтобы узнать, разрешено ли платежи (с помощью `SKPaymentQueue.CanMakePayments` ). 
-  Создать экземпляр `SKProductsRequest` с коды продуктов, определенные в iTunes Connect. Это делается в приведенном примере `InAppPurchaseManager.RequestProductData` метод. 
-  Вызовите метод Start на `SKProductsRequest` . При этом запускается асинхронный вызов к серверам App Store. Делегат ( `InAppPurchaseManager` ) будет вызван назад с результатами. 
-  Делегат ( `InAppPurchaseManager` ) `ReceivedResponse` метод обновляет пользовательский Интерфейс с использованием данных, возвращаемых из App Store (цены продукта & описания или сообщения о недопустимый продуктов). 

Общее взаимодействие выглядит следующим образом ( **StoreKit** встроена в iOS и **App Store** представляет серверы Apple):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Получение сведения о продукте graph")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Отображение пример сведений о продукте

[InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *расходных* пример кода демонстрирует, как можно получить сведения о продукте. В основном окне примера отображаются сведения для двух продуктов, которые получаются из App Store:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "В основном окне отображаются продукты сведения, полученные из App Store")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 Пример кода для извлечения и отображения сведений о продукте более подробно описывается ниже.


#### <a name="viewcontroller-methods"></a>Методы ViewController

`ConsumableViewController` Класс будет управлять Отображение цены на два продукта, идентификаторы которых продукта «зашиты» в классе.

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

В методе ViewWillAppear наблюдатель создается и назначается с помощью центра уведомлений по умолчанию:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 В конце `ViewWillAppear` мы вызываем метод `RequestProductData` метода для инициации запроса StoreKit. После этого запрос StoreKit асинхронно свяжется с серверами Apple для получения сведений и веб-канала вернитесь к своему приложению. Это достигается путем `SKProductsRequestDelegate` подкласс ( `InAppPurchaseManager`), о котором рассказывается в следующем разделе.

```csharp
iap.RequestProductData(products);
```

В коде для отображения цену и описание просто извлекает сведения из SKProduct и назначает его элементов управления UIKit (Обратите внимание, что мы отображаем `LocalizedTitle` и `LocalizedDescription` — StoreKit автоматически разрешает правильный текст и ценами на основе параметры учетной записи пользователя). В уведомлении, созданной ранее принадлежит следующий код:

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

`RequestProductData` Метод вызывается, когда приложение желает, чтобы для получения цены продукта и другие сведения. Он анализирует коллекцию идентификаторов продукта в правильный тип данных, затем создает `SKProductsRequest` с этой информацией. Вызов метода Start вызывает сетевой запрос к серверы Apple. Запрос будет выполняться асинхронно, а затем вызовите `ReceivedResponse` метод делегата при его успешном завершении.

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

iOS автоматически направит запрос на версию «песочницы» или «production» App Store, в зависимости от того, какой профиль подготовки, приложение выполняется с параметрами – поэтому при разработке или тестировании приложения запрос будет иметь доступ на каждый продукт настроено в iTunes Connect (даже те, которые еще не были отправлены или утверждены Apple). Когда приложение находится в рабочей среде, StoreKit запросов будет возвратить только сведения о **Approved** продуктов.   
   
   
   
 `ReceivedResponse` Переопределенный метод вызывается после серверы Apple стали делать с данными. Так как это вызывается в фоновом режиме, код должен проанализировать допустимые данные и использовать уведомления для отправки информации о продукте в любой ViewControllers, что «прослушивают» этого уведомления. Ниже приведен код для сбора сведений продукта и отправки уведомлений:

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

Несмотря на то, что не показано на схеме, `RequestFailed` , следует также переопределить метод, чтобы оставить отзыв можно предоставить пользователю, в случае серверов App Store, недоступны (или некоторых других ошибок). В примере кода просто выводит на консоль, но в реальном приложении можно запросить, чтобы `error.Code` свойство и реализовать пользовательское поведение (например, предупреждение для пользователя).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

На этом снимке экрана показан пример приложения сразу после загрузки (если доступен нет сведения о продукте):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "Пример приложения сразу после загрузки, когда отсутствует информация о продукте")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Недопустимый продуктов

`SKProductsRequest` Могут также возвращать список недопустимых идентификаторов продукта. Недопустимый продуктов обычно возвращаются из-за следующего вида:   
   
   
   
 **Идентификатор продукта этому** — принимаются только допустимые коды продуктов.   
   
 **Продукт не был утвержден** — во время тестирования, должен возвращать все продукты, очищенные для продажи `SKProductsRequest`; Однако в рабочей среде, возвращаются только утвержденные продукты.   
   
 **Идентификатор приложения не являются прямыми** — идентификаторы приложений подстановочный знак (со звездочкой) не допускают покупки из приложений.   
   
 **Неправильный профиль подготовки** — при внесении изменений в конфигурацию приложения на портале подготовки (например, включение покупки из приложений), не забудьте повторно создать и использовать нужный профиль подготовки, при сборке приложения.   
   
 **Контракт платных приложений iOS не подключены** — StoreKit компоненты не будут работать вообще, если отсутствует допустимый контракт для учетной записи разработчика Apple.   
   
 **Двоичный файл находится в состоянии Отклонено** — Если имеется ранее отправленные двоичный файл в отказанное (либо группой App Store или разработчиком) StoreKit компоненты не будут работать.

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

Цена уровни указать цену каждого продукта для всех магазинов международных приложений. Чтобы убедиться в правильном отображении цены по каждой валюте, используйте следующий метод расширения (определенные в `SKProductExtension.cs`) вместо того, чтобы свойство цена каждого `SKProduct`:

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

Код, который задает название кнопки используется метод расширения следующим образом:

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

С помощью двух разных iTunes тестовых учетных записей (по одному для American хранилища) и один для японского хранилища результатов в снимках экрана ниже:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Два разных iTunes тестовые учетные записи, показывающий языка определенные результаты")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 Обратите внимание на то, что хранилище, влияет на язык, который используется для валюты сведения и цену продукта, а устройства языковой параметр влияет на, метки и другие локализованное содержимое.   
   
   
   
 Помните, учетная запись, необходимо, чтобы использовать другое хранилище тестового **Выход** в **параметры > iTunes и App Store** и повторно запустить приложение для входа в систему с другой учетной записи. Чтобы изменить язык устройства перейдите к **параметры > Общие > International > язык**.

