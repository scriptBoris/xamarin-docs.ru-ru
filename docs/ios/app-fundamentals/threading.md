---
title: Работа с потоками в Xamarin.iOS
description: В этом документе описывается, как использовать интерфейсы API System.Threading в приложении Xamarin.iOS. В нем описывается библиотека параллельных задач, создания быстро реагирующих приложений и сбора мусора.
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: b90c59f09217077262c3aced9ee9e5d07849c25c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106589"
---
# <a name="threading-in-xamarinios"></a>Работа с потоками в Xamarin.iOS

Среда выполнения Xamarin.iOS предоставляет разработчикам доступ к .NET threading API-интерфейсы, и явным образом при работе с потоками (`System.Threading.Thread, System.Threading.ThreadPool`) и неявно в том случае, при использовании шаблонов асинхронного делегата или методы BeginXXX, а также полный диапазон интерфейсов API, поддерживающий Библиотека параллельных задач.



Xamarin настоятельно рекомендует использовать [библиотека параллельных задач](http://msdn.microsoft.com/library/dd460717.aspx) (TPL) для создания приложений по нескольким причинам:
-  Планировщик по умолчанию TPL делегирует выполнение задач в пул потоков, который в свою очередь, будет динамически увеличиваться количество потоков, которые нужны, так как процесс занимает место, избегая сценарии, где слишком много потоков итоге будут конкурировать за время ЦП. 
-  Проще думать об операциях с точки зрения задач TPL. Можно легко работать с ними, можно запланировать, сериализовать их выполнения или запуска многие параллельно с обширный набор API. 
-  Это основа для программирования с использованием C# async расширений языка. 


Медленно в пул потоков будет расти число потоков, при необходимости на основе числа ядер ЦП, доступных в системе, нагрузку на систему и потребности вашего приложения. Можно использовать этот пул потоков, вызывая методы в `System.Threading.ThreadPool` или с помощью по умолчанию `System.Threading.Tasks.TaskScheduler` (частью *Parallel Frameworks*).

Обычно разработчики используют потоки, при необходимости создания быстро реагирующих приложений и их не требуется блокировать основной пользовательский Интерфейс, запустить цикл.

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>Разработки быстро реагирующих приложений

Доступ к элементам пользовательского интерфейса должны быть ограничены в том же потоке, на котором выполняется основного цикла для вашего приложения. Если вы хотите внести изменения в основной пользовательский интерфейс из потока, должен очередь код с помощью [NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/type/Foundation.NSObject/), следующим образом:

```csharp
MyThreadedRoutine ()  
{  
    var result = DoComputation ();  

    // we want to update an object that is managed by the main
    // thread; To do so, we need to ensure that we only access
    // this from the main thread:

    InvokeOnMainThread (delegate {  
        label.Text = "The result is: " + result;  
    });
}
```

Оно вызывает код внутри делегата в контексте основного потока, избегая условия конкуренции, которые потенциально мог происходить сбой приложения.

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>Работа с потоками и сбора мусора

Во время выполнения среда выполнения Objective-C создайте и освобождения объектов. Если объекты помечаются для «auto выпуск», среда выполнения Objective-C выпустит таких объектов в потоке текущего `NSAutoReleasePool`. Xamarin.iOS создает одну `NSAutoRelease` пула для каждого потока из `System.Threading.ThreadPool` и для основного потока. Это по расширению охватывает все потоки, созданные с помощью TaskScheduler по умолчанию в System.Threading.Tasks.

При создании собственных потоков, с помощью `System.Threading` вы должны предоставить, вы являетесь владельцем `NSAutoRelease` пула для предотвращения утечки данных. Чтобы сделать это, просто являются оболочками потока в следующем фрагменте кода:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Примечание: Так как Xamarin.iOS 5.2 у вас нет для предоставления собственных `NSAutoReleasePool` больше одного будет предоставлен автоматически.


## <a name="related-links"></a>Связанные ссылки

- [Работа с потоком пользовательского интерфейса](~/ios/user-interface/ios-ui/ui-thread.md)
