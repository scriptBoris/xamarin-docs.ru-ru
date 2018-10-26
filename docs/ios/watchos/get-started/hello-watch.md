---
title: Привет, watchOS — Пошаговое руководство
description: Этот документ содержит пошаговое руководство по Создание приложения простой watchOS с помощью Xamarin. Он описывает способ работы в Visual Studio и Visual Studio для Mac, работать с раскадровками и реагировать на события в коде.
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 12/14/2016
ms.openlocfilehash: 7066acc82603b1c74dea2b7f0050bc63c46316f1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122237"
---
# <a name="hello-watchos--walkthrough"></a>Привет, watchOS — Пошаговое руководство

После создания решения, описанные в [установки и настройки](~/ios/watchos/get-started/installation.md), вы получите 3 проекта:

- Родительское приложение iOS, которая используется для установки или других административных задач на устройстве. (С другими типами расширения iOS, это часто называется «Контейнер» приложения.) С помощью приложения Watch станет возможно для пользователей запуска приложения Watch без **когда-нибудь** под управлением в родительское приложение;
- Расширение Контрольные значения, которое содержит программный код для приложения watch; и
- Приложение Контрольные значения, которое содержит ресурсы раскадровки и изображений, отображаемых в часы.

Убедитесь, что ваш [ссылки верны](~/ios/watchos/get-started/project-references.md): что родительское приложение имеет ссылку на расширение, и что у расширения есть ссылка в приложении для Apple Watch.

Убедитесь, что ваш пакет идентификаторы соответствовать \*.watchkitextension \*.watchkitapp соглашение и что файл Info.plist вашего расширения имеет **идентификатор пакета WKApp** присвоено идентификатору пакета Приложение для Apple Watch.

Вы сможете запустить приложение для Apple Watch сейчас, но так как файл раскадровки в приложение для Apple Watch не заполнено, невозможно определить.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![](hello-watch-images/projectstructure.png "В обозревателе решений")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-projectstructure.png "В обозревателе решений")

-----

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)
    
Дважды щелкните Interface.storyboard в приложение для Apple Watch запустить Xamarin iOS Designer (при наличии на компьютере Mac вы можете щелкнуть правой кнопкой и **открыть с помощью > конструктора Interface Builder Xcode**)


1.  Убедитесь, **элементов** и **свойства** панели видимы, но
1.  Щелкните, чтобы выбрать адаптер,
1.  Задайте идентификатор и название интерфейс контроллеру **interfaceController** и **Hi Watch**,
1.  Проверьте **класс** присваивается **InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "Задание идентификатора и заголовка интерфейса контроллера interfaceController и Hi Watch")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Дважды щелкните Interface.storyboard в приложение для Apple Watch для редактирования в конструкторе в Visual Studio IOS Xamarin:

1.  Откройте панель свойств;
1.  Измените класс на **InterfaceController**;
1.  Выберите контроллер интерфейса; и
1.  Задайте идентификатор и название интерфейс контроллеру **interfaceController** и **Hi Watch**.

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "Задание идентификатора и заголовка интерфейса контроллера interfaceController и Hi Watch")

-----


Создание пользовательского интерфейса:

1. Из **элементов** pad,
1. Перетаскивание **кнопку** и **метка** на сцене, и
1. Задайте в текст и атрибуты элементов управления, как показано:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![](hello-watch-images/draganddrop.png "Значение в текст и атрибуты элементов управления, как показано")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-draganddrop.png "Значение в текст и атрибуты элементов управления, как показано")

-----

1. Задайте **имя** в **свойства** панели для каждого элемента управления. В этом примере мы использовали `myButton` и `myLabel`.

1. Выбор кнопки на раскадровку и перейдите к **свойства** через панель **события** список, затем

1. Создайте новый **действие** , введя `OnButtonPress` и нажав клавишу **ввод**.
  Действие будет отображаться в списке, и разделяемый метод будет автоматически создан в C#.

![](hello-watch-images/buttonaction.png "Действие OnButtonPress добавить кнопки")

После сохранения раскадровки, **InterfaceController.designer.cs** обновляется имена элементов управления и действий... При открытии этого файла после обновления, можно увидеть как `RegisterAttribute` соответствующий контроллер и элементов управления пользовательского интерфейса соответствие C# экземпляр переменные, отмеченные `OutletAttribute` и как действия сопоставляют разделяемые методы, которые помечены `ActionAttribute`:

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac from the outlets and
// actions declared in your storyboard file.
// Manual changes to this file will not be maintained.
//
[Register ("InterfaceController")]
partial class InterfaceController
{
    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceButton myButton { get; set; }

    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceLabel myLabel { get; set; }

    [Action ("OnButtonPress:")]
    [GeneratedCode ("iOS Designer", "1.0")]
    partial void OnButtonPress (WatchKit.WKInterfaceButton sender);

    void ReleaseDesignerOutlets ()
    {
        if (myButton != null) {
            myButton.Dispose ();
            myButton = null;
        }
        if (myLabel != null) {
            myLabel.Dispose ();
            myLabel = null;
        }
    }
}
```

Теперь откройте **InterfaceController.cs** (*не* InterfaceController.designer.cs) и добавьте следующий код:

```csharp
int clickCount = 0;
partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}
```

Этот код должен быть достаточно прозрачно: переменная экземпляра `clickCount` — увеличивается каждый раз при функция `OnButtonPress` вызывается. Текст `myLabel` изменяется в соответствии с этой count; `myLabel`, само собой, — это имя одного из выходов, созданный в XCode. `partial` Функция представляет собой реализацию функции, связанные с именем действия, которые вы указали.

Если он еще не запускаемого проекта

1. Щелкните правой кнопкой мыши на проекте расширение просмотра и выберите **Назначить запускаемым проектом**,

1. Значение целевого объекта развертывания образа симулятор совместимые Kit Контрольные значения (например, iPhone 6 iOS 8.2),

1. Нажмите клавишу **Отладка** кнопки для вызова сборки и симулятора запуска.

    [![](hello-watch-images/readytodebug-sml.png "Элементы интерфейса Visual Studio")](hello-watch-images/readytodebug.png#lightbox)

При запуске симулятор, нажмите кнопку, чтобы увеличить метки.
Поздравляем, вы получите в свое приложение просмотра.

![](hello-watch-images/running.png "Приложение, работающее в симуляторе")


## <a name="related-links"></a>Связанные ссылки

- [Приступая к работе (пример)](https://developer.xamarin.com/samples/monotouch/WatchKit/GettingStarted/)
- [Настройка и установка](~/ios/watchos/get-started/installation.md)
- [Первое приложение просмотра видео](http://blog.xamarin.com/your-first-watch-kit-app/)
