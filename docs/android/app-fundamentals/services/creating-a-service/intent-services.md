---
title: Блокировка с намерением служб в Xamarin.Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 80849213649707615f8bd8e941e1a51c6b54e76e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763346"
---
# <a name="intent-services-in-xamarinandroid"></a>Блокировка с намерением служб в Xamarin.Android

## <a name="intent-services-overview"></a>Общие сведения о службах намерений

Оба работы и привязку службы выполняются в основном потоке, это означает, что для обеспечения производительности smooth требуются службе для асинхронного выполнения работы. Одним из самых простых способов решения этой проблемы является _рабочей очереди процессора шаблон_, где работу выполнять помещается в очередь, обслуживаемых одним потоком. 

[ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/) Является подклассом `Service` класс, предоставляющий Android определенного реализацией этого шаблона. Он будет управлять работой постановка в очередь, запуска рабочего потока для службы очереди, и извлечение запросов отключен очереди для запуска в рабочем потоке. `IntentService` Без вмешательства пользователя остановить себя и удалить рабочего потока при наличии объема работы, в очереди.
 
Рабочих отправляется в очередь, создав `Intent` и затем передачу, `Intent` для `StartService` метод.

Невозможно остановить или прервать `OnHandleIntent` метод `IntentService` во время его работы. Благодаря этой структуре `IntentService` должны храниться без сохранения состояния &ndash; не следует полагаться на активное подключение или подключения от остальной части приложения. `IntentService` Предназначен для statelessly обработки рабочих запросов.

Существует два требования для создания подкласса `IntentService`:

1. Новый тип (создан путем создания подкласса `IntentService`) переопределяет только `OnHandleIntent` метод.
2. Конструктор нового типа требует строку, которая используется для именования рабочий поток, который будет обрабатывать запросы. Имя этот рабочий поток в основном используется при отладке приложения.

В следующем коде показано `IntentService` реализацию переопределенный `OnHandleIntent` метод:

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

Работа отсылается `IntentService` путем создания экземпляра `Intent` и последующего вызова [ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/) метод с этого намерения в качестве параметра. Назначение для службы будут передаваться как параметр в `OnHandleIntent` метод. Этот фрагмент кода приведен пример отправки рабочего запроса целью. 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

`IntentService` Можно извлечь значения из значения, как показано в этом фрагменте кода:  

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
