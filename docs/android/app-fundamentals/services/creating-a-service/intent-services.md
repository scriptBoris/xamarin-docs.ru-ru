---
title: Службы Intent в Xamarin.Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1301f34ad1f7a0069c542ba81bf237a673fd239d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112862"
---
# <a name="intent-services-in-xamarinandroid"></a>Службы Intent в Xamarin.Android

## <a name="intent-services-overview"></a>Обзор службы Intent

Обе работы и привязку службы запускаются в основном потоке, это означает, что для сохранения производительности smooth, служба должна выполнять работу асинхронно. Одним из самых простых способов решения этой проблемы является с _рабочей очереди процессора шаблон_, где заданий для выполнения помещается в очередь, которая обрабатывается одним потоком. 

[ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/) Является подклассом `Service` класс, предоставляющий Android конкретной реализации этого шаблона. Он будет управлять работой постановка в очередь, запуска рабочего потока для службы очереди, и запросы на извлечение отключать очереди для запуска в рабочем потоке. `IntentService` Незаметно остановиться и удалить рабочий поток, при отсутствии больше работы в очереди.
 
Отправке работы в очереди, создав `Intent` и неудачами, `Intent` для `StartService` метод.

Невозможно остановить или прервать `OnHandleIntent` метод `IntentService` во время его работы. Благодаря этой структуре `IntentService` должны храниться без отслеживания состояния &ndash; его не следует полагаться на активное подключение или подключения от остальной части приложения. `IntentService` Предназначен для statelessly обработки рабочих запросов.

Существует два требования для создания подклассов `IntentService`:

1. Новый тип (создан путем создания подклассов `IntentService`) только переопределяет `OnHandleIntent` метод.
2. Конструктор для нового типа требует строку, которая используется для именования рабочий поток, который будет обрабатывать запросы. Имя этот рабочий поток в основном используется при отладке приложения.

В следующем коде показан `IntentService` реализацию переопределенный `OnHandleIntent` метод:

```csharp
[Service]
public class DemoIntentService: IntentService
{
    public DemoIntentService () : base("DemoIntentService")
    {
    }
    
    protected override void OnHandleIntent (Android.Content.Intent intent)
    {
        Console.WriteLine ("perform some long running work");
        ...
        Console.WriteLine ("work complete");
    }
}
```

Работа отсылается `IntentService` путем создания экземпляра `Intent` и последующего вызова [ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/) метод с этой целью как параметр. Цель передаются в службу в качестве параметра `OnHandleIntent` метод. В этом фрагменте кода приведен пример отправки рабочего запроса к объекту Intent. 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

`IntentService` Можно извлечь значения из намерения, как показано в этом фрагменте кода:  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");
    
    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```


## <a name="related-links"></a>Связанные ссылки

- [IntentService](https://developer.xamarin.com/api/type/Android.App.IntentService/)
- [StartService](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)
