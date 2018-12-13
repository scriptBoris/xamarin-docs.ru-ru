---
title: Класс MessagingCenter в Xamarin.Forms
description: В этой статье описывается, как использовать класс MessagingCenter в Xamarin.Forms для отправки и получения сообщений, что сокращает потребность в обеспечении взаимодействия между классами, такими как модели представлений.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 7fef4443cacba0fa8bdb8d5df070c4244730b4f5
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675183"
---
# <a name="xamarinforms-messagingcenter"></a>Класс MessagingCenter в Xamarin.Forms

_Платформа Xamarin.Forms включает в себя простую службу обмена сообщениями для отправки и получения сообщений._

<a name="Overview" />

## <a name="overview"></a>Обзор

Класс `MessagingCenter` в Xamarin.Forms позволяет моделям представлений и другим компонентам взаимодействовать, не зная ничего друг о друге, кроме простого контракта сообщения.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Как работает MessagingCenter

Класс `MessagingCenter` имеет два метода.

-  **Subscribe** — ожидает передачи сообщений с определенной сигнатурой и выполняет некоторое действие при их получении. Ожидать передачи одного и того же сообщения могут несколько подписчиков.
-  **Send** — публикует сообщение для прослушивателей. Если прослушиватели не подписаны на сообщение, оно игнорируется.


`MessagingService` — это статический класс с методами `Subscribe` и `Send`, которые используются в рамках всего решения.

Сообщения имеют строковый параметр `message`, который позволяет *адресовать* их. Методы `Subscribe` и `Send` используют универсальные параметры для дальнейшего управления доставкой сообщений: два сообщения с одинаковой строкой `message`, но разными аргументами универсального типа не могут быть доставлены одному подписчику.

Интерфейс API для `MessagingCenter` прост:

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

Эти методы рассматриваются далее.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>Использование класса MessagingCenter

Сообщения могут отправляться в результате действия пользователя (например, нажатия кнопки), системного события (например, изменения состояния элемента управления) или другого инцидента (например, завершения асинхронного скачивания). Получив сообщение, подписчик может изменить внешний вид пользовательского интерфейса, сохранить данные или инициировать другую операцию.

### <a name="simple-string-message"></a>Простое строковое сообщение

Простейшее сообщение содержит только строку в параметре `message`. Ниже показан метод `Subscribe`, *ожидающий передачи* простого строкового сообщения. Обратите внимание на то, что отправитель должен быть универсального типа `MainPage`. Любые классы в решении могут подписаться на сообщение с помощью следующего синтаксиса.

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

В классе `MainPage` приведенный ниже код *отправляет* сообщение. Параметр `this` — это экземпляр `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

Строка не меняется: в ней указывается *тип сообщения* и определяется, каких подписчиков следует уведомить. Сообщение такого рода служит для уведомления о наступлении определенного события, например завершения отправки, когда дополнительные сведения не требуются.

### <a name="passing-an-argument"></a>Передача аргумента

Чтобы передать аргумент с сообщением, укажите аргумент Type в универсальных аргументах метода `Subscribe` и в сигнатуре действия.

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

Чтобы отправить сообщение с аргументом, включите универсальный параметр Type и значение аргумента в вызов метода `Send`.

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

В этом простом примере используется аргумент `string`, но передать можно любой объект C#.

### <a name="unsubscribe"></a>Отмена подписки

Вы можете отменить подписку объекта на сигнатуру сообщения, чтобы сообщения больше не доставлялись. В синтаксисе метода `Unsubscribe` должна быть отражена сигнатура сообщения (поэтому может потребоваться включить универсальный параметр Type для аргумента сообщения).

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Сводка

Класс MessagingCenter — это простой способ сократить взаимодействие, особенно между моделями представлений. С его помощью можно отправлять и получать простые сообщения либо передавать аргументы между классами. Если классы больше не должны получать сообщения, необходимо отменить подписку на них.


## <a name="related-links"></a>Связанные ссылки

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Примеры Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
