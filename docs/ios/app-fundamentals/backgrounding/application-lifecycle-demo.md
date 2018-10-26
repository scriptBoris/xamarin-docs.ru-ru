---
title: Демонстрация жизненного цикла приложения для Xamarin.iOS
description: В этом документе рассматриваются различные события жизненного цикла, обрабатываемые делегата приложения в приложении iOS, демонстрирующий, как и когда эти события обрабатываются.
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/17/2018
ms.openlocfilehash: 3beb511c03b328ecea824bf89355d056df003f3e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102702"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>Демонстрация жизненного цикла приложения для Xamarin.iOS

В этой статье и [пример кода](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/) показано состояний четыре приложения в iOS, а роль `AppDelegate` методы в уведомление получить изменения состояния приложения. Каждый раз, когда приложение изменяет состояние, приложение выведет обновления для консоли:

[![](application-lifecycle-demo-images/image3-sml.png "Пример приложения")](application-lifecycle-demo-images/image3.png#lightbox)

[![](application-lifecycle-demo-images/image4.png "Каждый раз, когда приложение изменяет состояние, приложение выведет обновления для консоли")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>Пошаговое руководство

1. Откройте **жизненного цикла** в проекте **LifecycleDemo** решения.
1. Откройте `AppDelegate` класса. Методы жизненного цикла, чтобы указать, когда приложение изменяет состояние добавлено ведение журнала:

    ```csharp
    public override void OnActivated(UIApplication application)
    {
        Console.WriteLine("OnActivated called, App is active.");
    }
    public override void WillEnterForeground(UIApplication application)
    {
        Console.WriteLine("App will enter foreground");
    }
    public override void OnResignActivation(UIApplication application)
    {
        Console.WriteLine("OnResignActivation called, App moving to inactive state.");
    }
    public override void DidEnterBackground(UIApplication application)
    {
        Console.WriteLine("App entering background state.");
    }
    // not guaranteed that this will run
    public override void WillTerminate(UIApplication application)
    {
        Console.WriteLine("App is terminating.");
    }
    ```

1. Запустите приложение в симуляторе или на устройстве. `OnActivated` вызывается при запуске приложения. Приложение теперь находится в _Active_ состояния.
1. Нажмите кнопку «Home» на симулятор или устройство, чтобы перевести приложение в фоновом режиме. `OnResignActivation` и `DidEnterBackground` будет вызываться как переход приложения из `Active` для `Inactive` в `Backgrounded` состояние. Так как не включена приложение код для выполнения в фоновом режиме, оно считается _приостановлено_ в памяти.
1. Перейдите обратно к приложению для обеспечения ее на передний план. `WillEnterForeground` и `OnActivated` оба вызывается:

    ![](application-lifecycle-demo-images/image4.png "Изменения состояния, выводимого на консоль")

    Следующую строку кода в контроллер представления выполняется в том случае, когда приложение вошел переднего плана в фоновом режиме и изменяет текст, отображаемый на экране.

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. Нажмите клавишу **Главная** кнопку, чтобы поместить приложение в фоновом режиме. Затем дважды щелкните **Главная** , чтобы открыть приложение переключателя. На iPhone X, проведите по экрану вверх в нижней части экрана:

    [![Переключатель приложения](application-lifecycle-demo-images/app-switcher-sml.png "переключателя приложений")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. Найдите приложение в переключателе приложения и проведите вверх, чтобы удалить его (в iOS 11, длинное нажатие пока красный значки появляются в углу):

    [![Проведите по экрану снятия выполняемому приложению](application-lifecycle-demo-images/app-switcher-swipe-sml.png "считывание снятия выполняемому приложению")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

iOS, чтобы закрыть приложение. Обратите внимание, что `WillTerminate` не вызывается, поскольку приложение уже _приостановлено_ в фоновом режиме.

## <a name="related-links"></a>Связанные ссылки

- [LifecycleDemo (пример)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
