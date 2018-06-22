---
title: Написание отвечать на запросы приложений
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: b8c113b67b3fbfa57ca86c72e11ddeb0e4e1a9ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763505"
---
# <a name="writing-responsive-applications"></a>Написание отвечать на запросы приложений

Один из ключей для поддержания отвечать на запросы графического пользовательского интерфейса заключается в осуществлении длительно выполняемых задач в фоновом потоке, графический пользовательский Интерфейс не блокируются. Предположим, мы хотим вычисления значения для отображения для пользователя, но это значение имеет 5 секунд для вычисления:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        SlowMethod ();
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Это будет работать, но приложение «зависнет» для 5 секунд пока вычисляется значение. В течение этого времени приложение не будет отвечать на взаимодействие с пользователем. Чтобы обойти эту проблему, необходимо, чтобы наши вычисления в фоновом потоке:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Теперь мы можем вычислить значение в фоновом потоке, нашей графического интерфейса пользователя остается отвечать на запросы во время вычисления. Однако по завершении вычисления нашего приложения аварийно завершает работу, оставить этот параметр в журнале:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

Это так, как необходимо обновить графического пользовательского интерфейса из потока графического интерфейса пользователя. Наш код обновляет графического интерфейса пользователя в потоке пула потоков, что приводит к сбою приложения. Нам нужно нашей расчетов в фоновом потоке, но затем выполните наши обновления в потоке графического пользовательского интерфейса, которое обрабатывается с [Activity.RunOnUIThread](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)):

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        RunOnUiThread (() => textview.Text = "Method Complete");
    }
}
```

Этот код работает должным образом. Этот графический интерфейс пользователя продолжает работать и обновляется должным образом после вычисления сло.

Обратите внимание, что этот метод просто не используется для вычисления значения дорого. Он может использоваться для любого длительную задачу, может выполняться в фоновом режиме, как вызов веб-службы или загрузки данных в Интернете.
