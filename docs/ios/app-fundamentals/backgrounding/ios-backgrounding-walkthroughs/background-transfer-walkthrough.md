---
title: Фоновая служба передачи и NSURLSession в Xamarin.iOS
description: Этот документ содержит пошаговое руководство, которое демонстрирует использование фоновой передачи и NSUrlSession начать загрузку большого изображения и продолжать этого загруженного в том случае, если приложение размещается в фоновом режиме.
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4e525388290d92901e68e61f1ffa81866f5aac4d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114240"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>Фоновая служба передачи и NSURLSession в Xamarin.iOS

Фоновая передача инициируется путем настройки фона `NSURLSession` и постановки отправки или загрузки задачи. Если задание завершено, пока приложение в фоновом режиме, приостановлено или прервано, iOS сообщит приложению путем вызова обработчик завершения в приложении *AppDelegate*. На следующей диаграмме показано это в действии:

 [![](background-transfer-walkthrough-images/transfer.png "Фоновая передача инициируется, настроив на фоне NSURLSession и постановки отправки или загрузки задач")](background-transfer-walkthrough-images/transfer.png#lightbox)

Давайте посмотрим, как это выглядит в коде.

## <a name="configuring-a-background-session"></a>Настройка в фоновом сеансе

Чтобы убедиться в фоновом сеансе, создайте новый `NSUrlSession` и настройте его с помощью `NSUrlSessionConfiguration` объекта.

Определяет объект конфигурации сеанса, которые можно сделать и с видами задач, которые оно может работать.
Сеансов, настроенных с помощью `CreateBackgroundSessionConfiguration` метод будет запустить в отдельном процессе и выполнять на уровне пользователей (Wi-Fi) передач для сохранения данных и экономия энергопотребления.
В следующем образце кода показано правильной настройки сеанса передачи фона с помощью `CreateBackgroundSessionConfiguration` метод и уникальный идентификатор строки:

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, (NSUrlSessionDelegate) new MySessionDelegate(), new NSOperationQueue());

}
```

Помимо объект конфигурации сеанса также требует делегат сеанса и очереди.
Очереди определяет порядок, в котором выполняются задачи. Делегат сеанса chaperones процесс переноса и проверки подлинности маркеров, кэширование и других проблем, связанных с сеансами.

## <a name="working-with-tasks-and-delegates"></a>Работа с задачами и делегаты

Теперь, когда мы настроили фоновом сеансе, давайте начнем задач для обработки переноса. Мы можно хранить список этих задач с помощью `NSUrlSessionDelegate` экземпляра вызывается делегат сеанса. Делегат сеанса несет ответственность за вызов нулем или приостановленного приложения в фоновом режиме в маркер проверки подлинности, ошибки или завершении передачи.

`NSUrlSessionDelegate` Предоставляет следующие основные методы, чтобы проверить состояние передачи:

-  *DidFinishEventsForBackgroundSession* -этот метод вызывается после завершения всех задач и передача завершена.
-  *DidReceiveChallenge* — вызывается, чтобы запрос учетных данных, когда требуется авторизация.
-  *DidBecomeInvalidWithError* — вызывается, если `NSURLSession` становится недействительным.


Фон сеансы требуются более специализированный делегаты в зависимости от типов задач, работающих под управлением. Сеансы фона можно использовать два типа задач:

-  *Отправка задач* -задачи типа `NSUrlSessionUploadTask` использовать `NSUrlSessionTaskDelegate` , который наследует от `NSUrlSessionDelegate` . Этот делегат предоставляет отправить дополнительные методы для отслеживания хода выполнения, дескриптор перенаправление HTTP и многое другое.
-  *Загрузить задачи* -задачи типа `NSUrlSessionDownloadTask` использовать `NSUrlSessionDownloadDelegate` , который наследует от `NSUrlSessionTaskDelegate` . Этот делегат предоставляет все методы для отправки задачи, а также отслеживать ход выполнения скачивания и определить, когда задача скачивания имеет возобновления или завершения загрузки методов.


Следующий код определяет задачу, которая может использоваться для скачивания изображения из URL-адрес. Мы начнем задачи путем вызова `CreateDownloadTask` на наших фоновом сеансе и передачи в URL-адрес запроса:

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

Далее мы создадим делегат загрузки нового сеанса для отслеживания всех задач загрузки в этом сеансе:

```csharp
public class MySessionDelegate : NSUrlSessionDownloadDelegate
{
  public override void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
  {
    Console.WriteLine (string.Format ("DownloadTask: {0}  progress: {1}", downloadTask, progress));
    InvokeOnMainThread( () => {
      // update UI with progress bar, if desired
    });
  }
  ...
}
```

Если мы хотим узнать ход выполнения задачи загрузки, мы переопределяем `DidWriteData` способ отслеживания хода выполнения и даже обновления пользовательского интерфейса. Обновление элементов пользовательского интерфейса будет отображаться немедленно, если приложение находится на переднем плане, или будет ожидать пользователь при очередном открытии приложения.

API делегат сеанса — это широкий набор средств для взаимодействия с задачами. Полный список сеансов делегировать методам, обратитесь к `NSUrlSessionDelegate` документации по API.

> [!IMPORTANT]
> Фон сеансы запускаются в фоновом потоке, таким образом, все вызовы, чтобы обновить пользовательский Интерфейс должен быть явным образом выполнять в потоке пользовательского интерфейса путем вызова `InvokeOnMainThread` во избежание завершением работы приложения iOS. 


## <a name="handling-transfer-completion"></a>Обработка завершения передачи

Последним шагом является приложение знать, когда завершены все задачи, связанные с сеансом и обрабатывать новое содержимое.

В *AppDelegate*, Подпишитесь на `HandleEventsForBackgroundUrl` событий. Если приложение входит в фоновом режиме и выполняется сеанса передачи, этот метод вызывается и система передает нам завершающий обработчик:

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

Мы будем использовать обработчик завершения знать окончании наше приложение iOS для обработки.

Помните, что сеанс может породить несколько задач для обработки перемещения. После завершения последней задачи, приостановки или прекращен приложение повторно запущенное в фоновом режиме. Затем приложение повторно подключается к `NSURLSession` используя уникальный идентификатор сеанса и вызовы `DidFinishEventsForBackgroundSession` в делегате сеанса. Этот метод является приложения возможность обработать новое содержимое, включая обновление пользовательского интерфейса в соответствии с результатами перемещения:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

Когда все готово обработки нового содержимого, вызывается обработчик завершения, чтобы сообщить системе о том, что это можно сделать снимок приложение и вернитесь в спящий режим:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  var appDelegate = UIApplication.SharedApplication.Delegate as AppDelegate;

  // Handle new information, update UI, etc.

  // call completion handler when you're done
  if (appDelegate.backgroundSessionCompletionHandler != null) {
    NSAction handler = appDelegate.backgroundSessionCompletionHandler;
    appDelegate.backgroundSessionCompletionHandler = null;
    handler.Invoke ();
  }
}
```

В этом пошаговом руководстве мы рассматривали основные шаги по реализации фоновой службы передачи в iOS 7.



## <a name="related-links"></a>Связанные ссылки

- [Простой фоновой службы передачи (пример)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
