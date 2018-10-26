---
title: Написание быстро реагирующих приложений
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: a1642c4cbb790cf09d2a31e629408afc61d5b7ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121780"
---
# <a name="writing-responsive-applications"></a>Написание быстро реагирующих приложений

Один из ключей для поддержания быстрые графическим интерфейсом пользователя — для этого длительно выполняемых задач в фоновом потоке графического пользовательского интерфейса не блокируются. Предположим, что мы хотим вычисление значения для отображения пользователю, но это значение занимает 5 секунд для вычисления:

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

Это будет работать, но приложения будет «зависания» 5 секунд во время вычисления значения. В течение этого времени приложение не будет отвечать на любые взаимодействия с пользователем. Чтобы обойти эту проблему, мы хотим сделать наш вычисления в фоновом потоке:

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

Теперь мы вычисляем значение в фоновом потоке, поэтому наши графического пользовательского интерфейса продолжает работать во время вычисления. Тем не менее по завершении вычисления наше приложение аварийно завершает работу, оставляя это в журнале:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

Это обусловлено тем, необходимо обновить графического пользовательского интерфейса из потока графического интерфейса пользователя. Наш код обновляет графического пользовательского интерфейса в потоке ThreadPool, вызывает сбой приложения. Нам нужно вычисляет нашего значения в фоновом потоке, а затем выполните наши обновления в потоке графического пользовательского интерфейса, который обрабатывается с помощью [Activity.RunOnUIThread](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)):

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

Этот код работает должным образом. Этот графический интерфейс пользователя продолжает работать и обновляется должным образом после завершения вычисления.

Обратите внимание, что этот метод просто не используется для вычисления значение дорого. Он может использоваться для все длительные задачи, которые могут выполняться в фоновом режиме, как вызов веб-службы или загрузки данных в Интернете.
